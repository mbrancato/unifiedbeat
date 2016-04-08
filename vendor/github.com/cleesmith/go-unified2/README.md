***

### Feb 18, 2016:

### To properly handle a folder (spool) of unified2 files in [Unifiedbeat](https://github.com/cleesmith/unifiedbeat)

* [x] [Filebeat](https://github.com/elastic/beats)'s prospector/harvester approach is inappropriate for unifiedbeat
  * while unifiedbeat does work **as-is** today, changes should recognize:
    * the volume of data is a lot less than syslogs (given a well tuned sensor)
    * there's only one folder containing unified2 files
    * there's only one unified2 file that is **active** and should be **tailed**
  * of course, the above is all wrong if a server is used to centralize unified2 files from multiple sensors
    * then again, one could just run this version of unifiedbeat for each folder (i.e. each sensor) [KISS]
* [x] add ```os.Stat(r.reader.File.Name())``` to ```spoolrecordreader.go```
  * to deal with renamed or deleted files
* [x] change ```Offset()``` in ```spoolrecordreader.go``` to return full path not just ```path.Base```
* [x] add sample unified2 files in ```sample_data```
* [x] add examples
  * ```clsreadu2.go``` simple reader with counts
  * ```clsspoolreader.go``` to test **SpoolRecordReader**
    * use ```CloseHook``` to **rename** (_archive_) an indexed file
      * otherwise, an _endless reading loop_ occurs (```openNext``` flaw)
* [x] add new fields to the SpoolRecordReader struct:
  * ```FileSource``` - the file currently being tailed (read)
  * ```FileOffset``` - the offset position into FileSource
  * these fields allow for a registry file -- to bookmark where we were if interrupted
  * use these fields in ```openNext``` to set the offset in the call to ```NewRecordReader```

***
***

# go-unified2 [![GoDoc](https://godoc.org/github.com/jasonish/go-unified2?status.png)](https://godoc.org/github.com/jasonish/go-unified2)

A Go(lang) Library for decoding unified2 log files as generated by IDS
applications such as Snort and Suricata.

## Installation

```
go get github.com/jasonish/go-unified2
```

## Documentation

See https://godoc.org/github.com/jasonish/go-unified2

For more information on the unified2 file format see the
[Snort Manual](http://manual.snort.org/node44.html).