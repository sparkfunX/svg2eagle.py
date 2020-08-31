# svg2eagle.py
Python Port of Gordon Williams' "svg2eagle" Converter

```
usage: svg2eagle.py [-h] [-s SCALEFACTOR] [-l EAGLELAYERNUMBER]
                  [-v] [-o {b,ls,lib}] [-n SIGNALNAME] [-u SUBSAMPLING]
                  [-t TRACEWIDTH] [-a {tl,cl,bl,tc,cc,bc,tr,cr,br}]
                  imagePath

positional arguments:
  imagePath             Path to SVG image

optional arguments:
  -h, --help            show this help message and exit
  -s SCALEFACTOR        Text Height in inches (same as EAGLE text size value)
  -l EAGLELAYERNUMBER   Layer in EAGLE to create label into (default is tPlace
                        layer 21)
  -v                    Verbose mode (helpful for debugging)
  -o {b,ls,lib}         Output Mode ('b'=board script, 'ls'=library script,
                        'lib'=library file)
  -n SIGNALNAME         Signal name for polygon. Required if layer is not 21
                        (default is 'GND')
  -u SUBSAMPLING        Subsampling Rate (larger values provide smoother curves 
                        with more points)
  -t TRACEWIDTH         Trace width in mm
  -a {tl,cl,bl,tc,cc,bc,tr,cr,br}
                        Footprint anchor position (default:cl)
  -w {w,a}              Output writing mode (default:w)
  -d DESTINATION        Output destination filename (extension depends on -o
                        flag)                
  ```
  
  ## imagePath
  
  Your input image can be any SVG file and svgtoeagle will ATTEMPT to import all curves. However, for best results: 
  
  ### Convert all shapes to curves
  
  svgtoeagle.py is only able to read and convert line and poly objects, so shape primitives and text should all be converted to curves before svgtoeagle gets to touch it.
  
  ### No groups!
  
  Unlike the original svgtoeagle, which runs in a web browser, svgtoeagle.py never renders the input svg file. This means, unfortunately, that certain types of transforms get ignored. Technically, svgtoeagle.py will happily parse groups so long as there are no *group transforms*. The best way to avoid group transforms is simply to ungroup all curves in your SVG file. If a group is detected in your input file, svgtoeagle.py will still attempt to import the file but will also warn you that groups are present.
  
  ### Consider breaking up into smaller files
  
  Keep in mind that svgtoeagle will sample all curves in the file with the same resolution. If your file has multiple elements in it with very different sizes and "densities" then you may consider splitting them into separate svg files and then importing them with optimal subsampling (-u) factors for each. 
  
  ### How to use Inkscape to 'fix' an SVG file
  
  [Inkscape](https://inkscape.org/) is a free, open-source vector graphics program for all platforms that does a good job of creating SVG files that svgtoeagle.py likes. Here are the steps that I've found give satisfactory results:
  
  #### Step 1: Open your SVG File in Inkscape
  #### Step 2: Select All (Ctrl+A) and Ungroup (Ctrl+U) and Repeat until all groups are gone
  #### Step 3: Select All (Ctrl+A) and Convert Object to Path (Shift+Ctrl+C)
  #### Step 4: Save As Optimized SVG and select the "Collapse Groups" option before saving
 
 These steps are usually enough to convert an unfriendly SVG.
  
  ## SCALEFACTOR
  
  This argument controls the output size of the image. By default, SVG files that contain size information will convert 1:1
  
  ## EAGLELAYERNUMBER
  
  This argument controls which EAGLE layer the image is written to. Default value is 21 (tPlace)

  ## Verbose Mode
  
  If something gets borked, try running again with `-v` to see what's happening under the hood. If a job seems like it's taking too long, abort and then run in verbose mode to see if it's actually stuck or just very very long. Verbose mode is the closest that svgtoeagle.py has to a heartbeat. 
  
  ## Output Mode
  
  This argument controls which format the image output is generated for
  
  ### Board Script Mode
  
  By default, svgtoeagle.py will generate a file called `output.scr` which can be run in the board editor.
  
  ### Library Script Mode
  
  Library script mode will generate a file called `output.scr` which can be run in the library footprint editor.
  
  ### Library Package Mode
  
  Library package mode will generate a file called `output.lbr` which is an EAGLE library file containing the specified image
  
  ## SINGALNAME
  
  This argument defines the EAGLE signal name of the output, which is only required for metal layers. It is `GND` by default.
  
  ## SUBSAMPLING
  
  This argument essentially defines the resolution of the output. A smaller number will produce smoother curves but larger files. 
  **Note:**  Smaller scale factors require smaller subsampling factors
  
  ## TRACEWIDTH

  Tracewidth of output in mm
  
  ## Anchor Position
  
  In library package output mode, the position of the anchor point can be specified using the `-a` argument. The default value is `cl`
  The following values are permissible:
  
  ```
  tl - top left
  cl - center left
  bl - bottom left
  tc - top center
  cc - center center
  bc - bottom center
  tr - top right
  cr - center right
  br - bottom right
  ```
  
  ## Output Writing Mode

  By default, svgtoeagle.py with overwrite the output file. Running with the `-w a` option, however, will run buzzard.py in "append mode," 
  adding the specified image to the existing output file.
  
  ## Destination Filename
  
  Using the `-d` flag will allow you to specify the name of the output file. The file extension will automatically be selected based on
  the output format.
