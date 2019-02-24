---
title: Quickly get missing data in your shapefile fixed using QGIS
author: Anjesh
date: '2015-03-12'
slug: getting-qgis-fix-missing-region
---

TL;DR

I spent a day working in [QGIS](http://www.qgis.org/) to fix the missing layer in the shapefile. Follow the steps and treat yourself a day :) I am not a GIS person nor have i used QGIS extensively. The work was mostly search, try and test. Before I forget, here are the steps. 

<hr>

I see that [region geojson](https://github.com/anjesh/NepalMaps/blob/4a97f8a1d3d4dcfe4ef437b12294743f598194a2/geojson/regions.json) is missing some data.

<script src="https://embed.github.com/view/geojson/anjesh/NepalMaps/4a97f8a1d3d4dcfe4ef437b12294743f598194a2/geojson/regions.json"></script>

And i need is to patch that [missing region](https://github.com/anjesh/NepalMaps/blob/master/geojson/regions.json).

<script src="https://embed.github.com/view/geojson/anjesh/NepalMaps/master/geojson/regions.json"></script>

Fortunately the missing part is a [complete zone](https://github.com/anjesh/NepalMaps/blob/master/geojson/zones.json) that's available in another shapefile.

<script src="https://embed.github.com/view/geojson/anjesh/NepalMaps/master/geojson/zones.json"></script>

Here are the step-by-step instructions to merge the missing zone to the region in QGIS. I spent a day searching the internet, getting through the details, following what others has to say, explore on my own, try and try again. I am in no way a QGIS user or a GIS person, so I have used terms as i understand. 

I spent few hours in old version of QGIS before i realize that some functionalities were having problems. Ensure that you have the latest version of QGIS. 

Here's my system used to fix this problem *QGIS Version 2.8.1-Wien in MacOS X Yosemite V10.10*


Select `Layer > Add Layer > Add Vector Layer` to import the shapefile data. I imported [regions NPL_adm1](https://github.com/anjesh/NepalMaps/blob/master/baselayers/NPL_adm/NPL_adm1.shp) shapefile, which doesn't contain chuck of data.

![1.import.shp1](/post/2015-03-12-qgix-fix-missing-region/1.import.shp1.png "1.import.shp1")

The missing data is visible. That's the data we will be filling following this step by step process.

![2.imported.shp1](/post/2015-03-12-qgix-fix-missing-region/2.imported.shp1.png "2.imported.shp1")

Next I imported [zones NPL_adm2](https://github.com/anjesh/NepalMaps/blob/master/baselayers/NPL_adm/NPL_adm2.shp) data, which contains that missing data information.

![3.import.shp2](/post/2015-03-12-qgix-fix-missing-region/3.import.shp2.png "3.import.shp2")

All the zones, including the missing one, can be seen in NPL_adm2 data. You may turn the checkbox on and off on the left `Layers Panel` and see the layers getting hidden and displayed. 

![4.imported.shp2-done](/post/2015-03-12-qgix-fix-missing-region/4.imported.shp2-done.png "4.imported.shp2-done")

We need to create a separate layer of that missing part, which will be merged with the regions data. Select `View > Select > Select Feature(s)` and select the feature that we want to merge. After the selection, the feature gets highlighted and Copy the feature by `Edit > Copy Features`.

![5.select.missing-copy.feature](/post/2015-03-12-qgix-fix-missing-region/5.select.missing-copy.feature.png "5.select.missing-copy.feature")

Choose `Edit > Paste Features as > New Vector Layer` to create a new layer from that copied feature.

![6.paste.copied.feature.as.new-layer](/post/2015-03-12-qgix-fix-missing-region/6.paste.copied.feature.as.new-layer.png "6.paste.copied.feature.as.new-layer")

A set of popups appears, give the location to save the shapefile of the new layer.

![7.save.new.feature](/post/2015-03-12-qgix-fix-missing-region/7.save.new.feature.png "7.save.new.feature")

The `Layers Panel` now includes extra layer. I gave this layer a name `missing-region`.

![8.new.layer.created](/post/2015-03-12-qgix-fix-missing-region/8.new.layer.created.png "8.new.layer.created")

You may hide and show the layers from the `Layers Panel`. Below shows the layer which needs to be joined with our problematic shapefile `NPL_adm1`.

![9.see.only.new.layer](/post/2015-03-12-qgix-fix-missing-region/9.see.only.new.layer.png "9.see.only.new.layer")

Install MMQGIS plugin if you haven't installed. It's not present in default installation. Go to `Plugins > Manage and Install Plugins`, search for MMQGIS and install. You will see the MMQGIS in the menu bar. 

Select `MMQGIS > Combine > Merge Layers`. You will see all the layers you have in the current workspace. 

![10.mmqgis.merge](/post/2015-03-12-qgix-fix-missing-region/10.mmqgis.merge.png "10.mmqgis.merge")

I chose the layers `NPL_adm1` and `missing-region` and mentioned the filename for the new shapefile. 

![11.select.2.layers.merge.save](/post/2015-03-12-qgix-fix-missing-region/11.select.2.layers.merge.save.png "11.select.2.layers.merge.save")

You will get new merged layer, it contains the missing layer as well. Next I need to join that missing block to the region where it belongs. 

![12.merged.combined.layer.created](/post/2015-03-12-qgix-fix-missing-region/12.merged.combined.layer.created.png "12.merged.combined.layer.created")

Select both the features you want to merge. Select `View > Select > Select Feature(s)` and press `Command + left click` both the features - highlighted below. 

![13.select.layer.for.join](/post/2015-03-12-qgix-fix-missing-region/13.select.layer.for.join.png "13.select.layer.for.join")

Right click on the layer in the `Layers Panel` and choose `Toggle Editing`. You will see both the layers are now editable and are surrounded with thick red border.

![14.edit.layers.for.join](/post/2015-03-12-qgix-fix-missing-region/14.edit.layers.for.join.png "14.edit.layers.for.join")

Select `Edit > Merge Selected Features`. A popup appears. Select `Ok`.

![15.select.merge.selected.feature](/post/2015-03-12-qgix-fix-missing-region/15.select.merge.selected.feature.png "15.select.merge.selected.feature")

Congratulations! The missing block is now filled in the shapefile and part of the region. 

![16.merged](/post/2015-03-12-qgix-fix-missing-region/16.merged.png "16.merged")

