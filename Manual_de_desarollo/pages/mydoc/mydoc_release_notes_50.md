---
title: Release notes 5.0
# tags: [getting_started]
# keywords: release notes, announcements, what's new, new features
# last_updated: July 3, 2016
# summary: "Version 5.0 of the Documentation theme for Jekyll changes some fundamental ways the theme works to provide product-specific sidebars, intended to accommodate a site where multiple products are grouped together on the same site rather than generated out as separate outputs."
sidebar: mydoc_sidebar
permalink: mydoc_release_notes_50.html
folder: mydoc
---

La codificación geográfica es el proceso de convertir direcciones (como "Beauchef 850, Santiago") en coordenadas geográficas (como latitud-33.4577 y longitud-70.6636), que se puede usar para colocar marcadores en un mapa o posicionar el mapa.  

Geocoding funciona como una request api tipo GET usando la siguiente forma:

```
https://maps.googleapis.com/maps/api/geocode/outputFormat?parameters
```

donde el output format debe puede ser json o xml.

Para el caso de buscar la facultad, la variable parameters debiese ser:
```
address=850+Beauchef,+Santiago,+Chile
```

donde el resultado viene dado  por :
``` javascript
{
   "results": [
       {
           "address_components": [
               {
                   "long_name": "850",
                   "short_name": "850",
                   "types": [
                       "street_number"
                   ]
               },
               {
                   "long_name": "Beauchef",
                   "short_name": "Beauchef",
                   "types": [
                       "route"
                   ]
               },
               {
                   "long_name": "Santiago",
                   "short_name": "Santiago",
                   "types": [
                       "locality",
                       "political"
                   ]
               },
               {
                   "long_name": "Santiago",
                   "short_name": "Santiago",
                   "types": [
                       "administrative_area_level_3",
                       "political"
                   ]
               },
               {
                   "long_name": "Santiago",
                   "short_name": "Santiago",
                   "types": [
                       "administrative_area_level_2",
                       "political"
                   ]
               },
               {
                   "long_name": "Región Metropolitana",
                   "short_name": "Región Metropolitana",
                   "types": [
                       "administrative_area_level_1",
                       "political"
                   ]
               },
               {
                   "long_name": "Chile",
                   "short_name": "CL",
                   "types": [
                       "country",
                       "political"
                   ]
               }
           ],
           "formatted_address": "Beauchef 850, Santiago, Región Metropolitana, Chile",
           "geometry": {
               "location": {
                   "lat": -33.4575249,
                   "lng": -70.6622022
               },
               "location_type": "ROOFTOP",
               "viewport": {
                   "northeast": {
                       "lat": -33.4561759197085,
                       "lng": -70.66085321970849
                   },
                   "southwest": {
                       "lat": -33.4588738802915,
                       "lng": -70.66355118029151
                   }
               }
           },
           "place_id": "ChIJA6jyZQPFYpYRI7VZmK3t3x0",
           "plus_code": {
               "compound_code": "G8RQ+X4 Santiago, Chile",
               "global_code": "47RFG8RQ+X4"
           },
           "types": [
               "establishment",
               "point_of_interest"
           ]
       }
   ],
   "status": "OK"
}

```
Para implementarlo en maps javascript , se debe crear un buscador dentro del mapa que se pueda escribir un input como dirección para que se vea mas o menos asi:

<!-- foto -->
{% include image.html file="Geocoding.png"  alt="Jekyll" caption="Visualizacion para los usuaries" %}


Debe de ser intuitivo para el usuario buscar una dirección. Para implementarlo en el mapa se debe inicializar la función geocoder e iterar sobre objetos como InputText, submitButton y clear button. En esta parte solo se inicializa, no se escoge en qué parte aparecerán.

```javascript
geocoder = new google.maps.Geocoder();

inputText.type = "text";
 inputText.placeholder = "Enter a location";
 
 const submitButton = document.createElement("input");
 
 submitButton.type = "button";
 submitButton.value = "Buscar";
 submitButton.classList.add("button", "button-primary");
 
 const clearButton = document.createElement("input");
inputText.type = "text";
 inputText.placeholder = "Enter a location";
 
 const submitButton = document.createElement("input");
 
 submitButton.type = "button";
 submitButton.value = "Buscar";
 submitButton.classList.add("button", "button-primary");
 
 const clearButton = document.createElement("input");
```
Luego se crea la función geocode que tendrá como objetivo realizar la api request para conseguir las coordenadas de la dirección ingresada.
```javascript
unction geocode(request: google.maps.GeocoderRequest): void {
 clear();
 
 geocoder
   .geocode(request)
   .then((result) => {
     const { results } = result;
 
     map.setCenter(results[0].geometry.location);
     marker.setPosition(results[0].geometry.location);
     marker.setMap(map);
     responseDiv.style.display = "block";
     response.innerText = JSON.stringify(results[0].geometry.location.lat(), null, 2);
})
   .catch((e) => {
     alert("Geocode was not successful for the following reason: " + e);
   });

```

Con estas líneas, se busca la dirección, se centra el mapa en donde se encuentra la dirección y se imprime lo que devuelve la API.

<!-- In previous versions of the theme, I built the theme to generate different outputs for different scenarios based on various filtering attributes that might include product, version, platform, and audience variants.

However, this model results in siloed outputs and lots of separate file directories to manage. Instead of having 30 separate sites for your content (or however many variants you might have been producing), in this version of the theme I've moved towards a strategy of having one site with multiple products.

For each product, you can associate a unique sidebar with each of the product's pages. This allows you to have all your documentation on one site, but with separate navigation that is tailored to a view of that product.

You can still output to both web and PDF. And if you really need multiple site outputs, you can still do so by using multiple configuration files that trigger different builds. But my conclusion after using the multiple site output model for some years is that it's a bad practice for tech comm.

## Permalinks

With this theme, since you'll be publishing to one site, I've implement permalinks instead of relative links. Using permalinks means the way you store pages is much more flexible. You can store topics in folders and subfolders, etc., to any degree. But note that with permalinks you can't view the content offline (outside of Jekyll's preview server) nor on a separate site other than the one specified in the configuration file. Permalinks are how Jekyll was designed to work, and the sites just work better that way.

## Kramdown and Rouge

I also switched from redcarpet and Pygments to Kramdown and Rouge to align with the current direction of Jekyll 3.0. Kramdown is a Markdown filter (it's slightly different from Github-flavored Markdown). Rouge is a syntax highlighter. Pygments had some dependencies on Python, which made it more cumbersome for Windows users.

## Blog feature

I included a blog feature with this version of the theme. You can write posts and view them through the News link. There's also an archive for blog posts that sorts posts by year.

Additionally, the tagging system works across both the blog and pages, so your tags allow users to move laterally across the site based on topics they're interested in. When you view a tag archive, the sidebar shows a list of tags.

## Updated documentation

I updated the documentation for  the theme. The switch from the multi-site outputs to the single-site with multiple sidebars required updating a lot of different parts of the documentation and code.

## Fixed errors

Previously I had some errors with the HTML that showed up in w3c HTML validator analyses. This caused some small problems in certain browsers or systems less tolerant of the errors. I fixed all of the errors.

## Accessing the old theme

If you want to access the old theme, you can still find it [here](https://github.com/tomjoht/jekylldoctheme-separate-outputs). -->

{% include links.html %}
