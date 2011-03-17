# formaline

 formaline is a new (node.js) module for handling simple form posts and fast parsing of file uploads,
 it is ready (I think) for integration with connect.js  

## Installation
     
with npm:
    $ npm install formaline

with git:
    $ git clone git://github.com/rootslab/formaline.git

## Features
  
  in progress..
  
 - It is possible to create instances via configuration object
 - Useful configuration parameters (like maxBytes..)
 - Fluid exceptions handling
 - Many events for total control of parsing flow 
 - Very Fast and Simple Parser (see parser-benchmarks)
 - It is possible to preserve or auto-remove incomplete files upload, due to exceeding of max bytes limit 
 - It is possible to easily integrate with connect.js
 - It is not perfect, who is ?
 
 etc..
 
 (the parsing data rate depends on many factor, it is possible to reach 700 MB/s and more with a *real* Buffer totally loaded in RAM and a basic ~60 bytes boundary string (like Firefox uses), is not the same thing with a real chunked data upload, as others libraries announce,)


## Usage

*module usage:*

    var formaline = require('formaline');

    var config = {
        
            /*
             temporary upload directory for file uploads
             for every upload request a subdirectory is created that contains receiving files 
             default is /tmp/ -->
            */
            
        tmpUploadDir: '/var/www/upload/',
            
            /*
             default is false, or integer chunk factor, 
             every k chunk emits 'dataprogress' event  start from first chunk ( 1+(0*k) 1+(1*k),1+(2*k),1+(3*k) ), 
             minimum multply factor value is 2 -->
            */
            
        emitDataProgress: !true, //3,10,100 (every k chunks)
            
            /*
             max bytes allowed, this is the max bytes writed to disk before stop to write 
             this is also true for serialzed fields not only for files upload  -->
            */
            
        maxBytes: 3949000, //bytes ex.: 1024*1024*1024 , 512
        
            /*
             default false, bypass headers value, continue to write to disk  
             until maxBytes bytes are writed. 
             if true -> stop receiving data, when headers Content-Length exceeds maxBytes
            */
            
        blockOnReqHeaderContentLength: !true,
        
            /*
             remove file not completed due to maxBytes, 
             if true formaline emit fileremoved event, 
             otherwise return a path array of incomplete files when 'end' event is emitted 
            */
            
        removeIncompleteFiles : true,
        
            /*
             enable various logging levels
             it is possible to switch on/off one or more levels at the same time
             debug: 'off' turn off logging
            */
            
        logging: 'debug:on,1:on,2:on,3:off' //string ex.: 'debug:on,1:off,2:on,3:off'
            
            /*
            listeners
            */
            
        listeners: {
            'warning': function(msg){
                ...
            },
            'headersexception': function ( isUpload, errmsg, res, next ) {
                ...
                next();               
            },
            'exception': function ( isUpload, errmsg, res, next ) {
                ...
                next();
            },
            'filepathexception': function ( path, errmsg, res, next ) {
                ...
                next();
            },
            'field': function ( fname, fvalue ) {
                ...
            },
            'filereceived': function ( filename, filedir, ctype, filesize ) {
                ...
            },
            'fileremoved': function ( filename, filedir ) {
                ...
            },
            'dataprogress': function ( bytesReceived, chunksReceived ) {
                ...
            },
            'end': function ( incompleteFiles, response, next ) {
                response.writeHead(200, {'content-type': 'text/plain'});
                response.end();
                //next();
            }
        }
    };
        
    /*
        create an instance of formaline with configuration object and parse req
    */
    
    new formaline( config ).parse( req, res, next );


 **See Also :**
 - examples directory for seeing formaline in action. 
 - parser-benchmarks directory for testing algorithm parsing speed (wow). 
  
## API

 in progress..

## License 

(The MIT License)

Copyright (c) 2011 Guglielmo Ferri &lt;44gatti@gmail.com&gt;

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
'Software'), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
