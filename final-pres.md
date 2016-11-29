<style>
.footer {
    color: black;
    background: #E8E8E8;
    position: fixed;
    top: 90%;
    text-align:center;
    width:100%;
}
.midcenter {
    position: fixed;
    top: 15%;
    left: 25%;
}
.small-code pre code {
  font-size: 1em;
}

.reveal h3 {
  word-wrap: normal;
  -moz-hyphens: none;
}
.reveal h1 {
  word-wrap: normal;
  -moz-hyphens: none;
}
</style>



Mapping Science Stories
========================================================
author: Katie MacIntosh
date: November 29th, 2016
autosize: true
font-family: 'Helvetica Neue'


========================================================

<a href="http://anecdotalevidence.ca/">
<div class = "midcenter"> 
<img src="AE-logo.png"></img>
</div>
</a>

========================================================


- Manually input the location mentioned in each story 

- Created .csv with different scales, ie. "Location", "Country", etc.

- Concatenated to create one 'to_geocode' column


========================================================



```
 [1] " Berlin  Germany"                                            
 [2] "Science World Vancouver British Columbia Canada"             
 [3] "  Sao Paulo State Brazil"                                    
 [4] " Onagawa  Japan"                                             
 [5] " Stehekin Washington USA"                                    
 [6] "Van Dusen Botanical Garden Vancouver British Columbia Canada"
 [7] " Squamish British Columbia Canada"                           
 [8] "Vancouver Aquarium Vancouver British Columbia Canada"        
 [9] "   France"                                                   
[10] "   Kenya"                                                    
[11] "Lonsdale Quay North Vancouver British Columbia Canada"       
[12] "Ohlone Regional Wilderness  California USA"                  
[13] "Mt. Baker  Washington USA"                                   
[14] "  British Columbia Canada"                                   
[15] "Jatun Sacha   Ecuador"                                       
[16] " Vancouver British Columbia Canada"                          
[17] "Kluane National Park  Yukon Territory Canada"                
[18] "  Sabah Malaysia"                                            
[19] " Vancouver British Columbia Canada"                          
[20] " Vancouver British Columbia Canada"                          
[21] "  Western Australia Australia"                               
[22] "1100 Chestnut St Vancouver British Columbia Canada"          
[23] "Bamfield  British Columbia Canada"                           
[24] "2800 Bullittsburg Church Rd Petersburg Kentucky USA"         
[25] "Yellowstone National Park  Wyoming USA"                      
[26] "  Western Australia Australia"                               
[27] "   Costa Rica"                                               
```

========================================================
- Used geocode function from 'ggmap' package; uses Google API


```r
# geocode
storymap_geocoded <- storymap_df %>% 
  as.data.frame() %>% 
  mutate_geocode(to_geocode)
```


```r
storymap_geocoded$lat
```

```
 [1]  52.520007  49.273450 -23.543179  38.449733  48.309303  49.238444
 [7]  49.701634  49.300144  46.227638  -0.023559  49.327776  37.526997
[13]  47.583237  53.726668  -1.085876  49.282729  60.750000   5.978840
[19]  49.282729  49.282729 -27.672817  49.276367  48.833289  39.085998
[25]  44.427968 -27.672817   9.748917
```

========================================================

<div class = "midcenter"> 
How to connect to the website?
</div>

========================================================

- Already have all of the story .mp3s on the website
- Added these to .csv and created .html that would shows a  popup when locations are clicked


```r
# template for blog link
blog_link <- paste0("<h3><a href='%s' style='text-decoration:none'>",
                    "%s</a></h3>")
# template for audio player
player <- paste0("<audio controls style='width:100%%'>",
                 "<source src='%s' type='audio/mpeg'></audio>")
storymap_popup <- storymap_geocoded %>% 
  # generate html for popup
  mutate(popup_html = paste0(sprintf(blog_link, link, storyteller),
                      "<p>", description, "</p>",
                      sprintf(player, audio_file)))
```

========================================================




```r
#creating the map!
m <- leaflet(storymap_shifted) %>% 
  addProviderTiles("Esri.WorldTerrain") %>% 
  addCircleMarkers(lng = ~lon, lat = ~lat, radius = 8, 
              stroke = TRUE, color = '#aaa', weight = 1, 
              fillColor = '#69d2e7', fillOpacity = 0.8,
              popup = ~popup_html,
              clusterOptions = markerClusterOptions
              (showCoverageOnHover = FALSE,
              zoomToBoundsOnClick = TRUE,
#this function accesses .js and changes the colour of the clustering
              iconCreateFunction=JS("function (cluster) {           
var childCount = cluster.getChildCount();  
                 if (childCount < 100) {  
                  c = 'rgba(45, 132, 200, 1.0);'
                } else { 
                  c = 'rgba(241, 128, 23, 1);'  
                }    
return new L.DivIcon({ html: '<div style=\"background-color:'+c+'\"><span>' 
+ childCount + '</span></div>', className: 'marker-cluster', 
iconSize: new L.Point(40, 40) });
  }")))
```

========================================================




```
Error in loadNamespace(name) : there is no package called 'webshot'
```
