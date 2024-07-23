## Browser API ##

After initialization, the browser can be controlled using the functions described below. 

* [loadGenome(config)](#loadgenome)
* [loadSessionObject(session)](#loadsessionobjectsession)
* [loadSession({url})](#loadsessionurl)
* [loadTrack(config)](#loadtrackconfig)
* [findTracks(propertyOrFunction, value)](#findtrackspropertyorfunction-value)
* [removeTrackByName(name)](#removetrackbynamename)
* [removeTrack(track)](#removetracktrack)
* [loadROI(configOrArray)](#loadroiconfigorarray)
* [clearROIs()](#clearrois)
* [getUserDefinedROIs()](#getuserdefinedrois)
* [search(symbol)](#searchsymbol)
* [zoomIn()](#zoomin)
* [zoomOut()](#zoomout)
* [currentLoci()](#currentloci)
* [visibilityChange()](#visibilitychange)
* [toJSON()](#tojson)
* [compressedSession()](#compressedsession)
* [toSVG()](#tosvg)
* [setCustomCursorGuideMouseHandler(handler)](#setcustomcursorguidemousehandlerhandler)

### loadGenome ###

Returns a promise to load a reference genome.  
See the [Reference Genome](https://github.com/igvteam/igv.js/wiki/Reference-Genome) for more detail on configuration options.

Example

```
browser.loadGenome(
{
    "id": "hg38",
    "name": "Human (GRCh38/hg38)",
    "fastaURL": "https://s3.amazonaws.com/igv.broadinstitute.org/genomes/seq/hg38/hg38.fa",
    "indexURL": "https://s3.amazonaws.com/igv.broadinstitute.org/genomes/seq/hg38/hg38.fa.fai",
    "cytobandURL": "https://s3.amazonaws.com/igv.broadinstitute.org/annotations/hg38/cytoBandIdeo.txt",
    "tracks": [
      {
        "name": "Refseq Genes",
        "url": "https://s3.amazonaws.com/igv.org.genomes/hg38/refGene.txt.gz",
        "order": 1000000,
        "indexed": false
      }
    ]
  }
}
```

### loadSessionObject(session) ###

Load a session object, also referred to as a browser configuration object.   See [Browser Creation](Browser-Creation) for more details.   Loading a session will clear the current reference genome and all tracks.

### loadSession(url) ###

Load a session by url, which should point to a valid session json object.  The json is a representation of a browser configuration object described in the [Browser Creation](Browser-Creation) section, but does not support parameter values not representable as json such as functions and promises.

### loadTrack(config) ###

Returns a promise to load and configure a track.
See the [Tracks page](tracks/Tracks.md) for more detail on configuration options.

```
browser.loadTrack({
  url: 'http://data.broadinstitute.org/igvdata/1KG/b37/data/HG02450/alignment/HG02450.mapped.ILLUMINA.bwa.ACB.low_coverage.20120522.bam',
  label: 'HG02450'
    })
.then(function (newTrack) {
  alert("Track loaded: " + newTrack.name;
})
.catch(function (error)  {
   // Handle error
})
 ```

### findTracks(propertyOrFunction, value) ###

```
            console.log("Find tracks by ID 'T2':");
            const tracksById = browser.findTracks("id", "T2");
            for(let t of tracksById) {
                console.log(`  id=${t.id}   name=${t.name}`);
            }

            console.log("Find tracks by type 'wig'");
            const tracksByType = browser.findTracks("type", "wig");
            for(let t of tracksByType) {
                console.log(`  id=${t.id}   name=${t.name}`);
            }

            console.log("Find tracks by function function(track) {return track.name && track.name.startsWith('GM128')}");
            const tracksByFunction = browser.findTracks(function(track) {
                return track.name && track.name.startsWith('GM128');
            });
```

### removeTrackByName(name)  ###

Remove track(s) whose "name" property matches the given name.  

### removeTrack(track) ###

### loadROI(configOrArray) ###

Returns a promise to load an annotation file or array of annotation files to define regions of interest (ROIs).  Regions of interest are overlaid on the genome view across all tracks.  

```
browser.loadROI([
                        {
                            name: 'ROI set 1',
                            url: 'https://s3.amazonaws.com/igv.org.test/data/roi/roi_bed_1.bed',
                            indexed: false,
                            color: "rgba(68, 134, 247, 0.25)"
                        },
                        {
                            name: 'ROI set 2',
                            url: 'https://s3.amazonaws.com/igv.org.test/data/roi/roi_bed_2.bed',
                            indexed: false,
                            color: "rgba(0, 150, 50, 0.25)"
                        }
```
### clearROIs() ###

Remove all regions of interest.

### getUserDefinedROIs() ###

Returns a promise to return an array of user defined features.

### search(symbol) ###
       
Search by annotation symbol or locus string.

```
   browser.search('EGFR');
```
By default the search function uses a webservice to query positions of RefSeq genes for genome "hg19".  This can be overriden during initialization by supplying a url to a custom webservice.  For details see [Browser initialization](https://github.com/igvteam/igv.js/wiki/Browser).

Additionally, one or more annotation tracks can be used to back the "search" function by setting the searchable property to true.  See [Tracks](https://github.com/igvteam/igv.js/wiki/Tracks) for more details.

The search function can also be passed an explicit location,  for example
```
   browser.search('chr10:1000-2000')
```

Multiple loci can be passed as a space delimited list.  This will result in a multi-locus view.

```
browser.search('chr10:1000-2000' 'EGFR')
```

This function returns ```true``` if the symbol was found, ```false``` otherwise.

### zoomIn() ###

Zoom in by a factor of 2 
```
    browser.zoomIn();
```

### zoomOut() ###

Zoom out by a factor of 2 
```
    browser.zoomOut();
```

### currentLoci()

Return the current genomic region as a locus string, or multiple regions if in multi-locus view

### visibilityChange() ###

Signal a change in visibility, typically from hidden  (e.g. display:none) to shown  (e.g. display:block).  This is necessary because changes in display attribute do not trigger events.
```
    igv.visibilityChange();
```
To target a specific igv browser instance
```
    browser.visibilityChange();
```     

### toJSON() ###

Return the current state of the browser as a JSON object.  This object can be loaded with loadSessionObject.

```
   const json = browser.toJSON();

   browser.loadSessionObject(json)

```

### compressedSession() ###

Return a compressed, encoded, string representing the current browser state.   This string can be used to load the session on any page hosting an igv.js instance with a url of the form

```
https://myhost/mypage?sessionURL=blob:<compressed session string>
```

Note to reinstate the session ```queryParametersSupported``` must be set to true in the igv.js configuration.

### toSVG() ###

Convert the browser contents to SVG format. This includes ideogram, ruler, and all tracks. The value returned by this function is a serialized SVG object.
```
    browser.toSVG();
```



### setCustomCursorGuideMouseHandler(handler) ###

Pass genomic location and mouse location with respect to trackContainer to the provided handler.
The data is transmitted as the cursor guide is manipulated across the track container.

Parameters:
bp - cursor-guide location. base-pair units.
start - track start location. base-pair units.
end - track end location. base-pair units.
interpolant - cursor-guide location. 0 - 1 units.
```
    browser.setCustomCursorGuideMouseHandler(({ bp, start, end, interpolant }) => {  });
```