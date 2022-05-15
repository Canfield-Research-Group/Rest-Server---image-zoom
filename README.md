# Rest Server - image-zoom Migration

Ancient site but still used on www.canfield.com astronomy. Migrating off
of CRG NODE server so need to isolate into its own REPO.

The zoom.px script has not been used in ages and ages and was moved out
of the 'image-zoom' path to root to isolate it.

image-zoom : is a path (https://rest.canfield.com/image-zoom) that is used
by the current www.canfield.com (weebly) as a path to fetch images. So retain
that path.

Target: CloudFlare pages for 'image-zoom' but we will see if that works. It may
require tweaks to weebly site overrides.

Conversion of dynamic (using image-zoom.px) to static

- Re-arrange code to place static content in a directory suitable for building
  - Goal: Avoid changing path of 'image-zoom' and polluting the build area with any non-static data
  - Move `image-zoom/zoom.px` to root, it will eventually get a redirect or new directory
  - Create `www` this will become the 'build' directory and root for all 'pages'
  - Move `image-zoom` to `www`
  - When deploying the site to target use `www` as the BASE directory and then image-zoom will
  work as Weebly expects.
  - **TODO**
    - Use GitHub actions OR CluodFlare builder to take astronomy image uploaded to GIT
    and perform the `zoom.px` work on them during build.
      - An ACTION will only work if we move to 'devel' branch and then use action to compile everything
      and have that push t master, which triggers CloudFlare deployment.
      - **Weebly** then loads the HTML inline to the page OR restore the OLD JSON
      code but just load the JSON elements instead of HTML5 (but I cannot confirm this works reliable)
      - **Or**  use a diffetent static build
- Developer Box (PopOS in my case)
  - Fetch Moon JSON data (static) from existing Docker application
    - See [Moon Static](#moon-static) for instructions and example
    - Replace the [JS code](#moon) in Astronomy page with the HTML so that that the HTML si 
    in the div.
  - Fetch Planet JSON data (static) from existing Docker application
          - See [Planet HTML](#planets-static)
      - Replace the JS code in Astronomy page with the HTML so that that the HTML si
        in the div.
        - See old JS: [Planet JS](#planets-dynamic-deprecated)

- Add an extra HTML code section after planets to initialize the Magic Zoom (ANCIENT Version)
  - `<script>MagicZoomPlus.refresh();</script>`


# Moon Files

## Moon Static

- Fetch from live site (before it was deactivated)
  - `curl http://rest.canfield.com/image-zoom/zoom.px/astronomy/moon > moon.json`
- Filter out just the HTML
  - `jq .data.html moon.json`
- Used IntelliJ to format
  - Tip: IntelliJ editor reformts works fine within Markdown fence with html tag.

```html
<div id='crg-moon'>
  <style> .crg-zoom-cell {
    display: inline;
    padding: 5px;
  }

  </style>
  <div class='crg-zoom'>
    <div class='crg-zoom-row'>
      <div class='crg-zoom-cell'><a href='http://rest.canfield.com/image-zoom/images/astronomy/moon/Colombo-Labels.jpg'
                                    class='MagicZoomPlus' rel='zoom-position: inner'><img
              src='http://rest.canfield.com/image-zoom/images/astronomy/moon/Colombo-Labels.thumb.jpg'/></a></div>
      <div class='crg-zoom-cell'><a
              href='http://rest.canfield.com/image-zoom/images/astronomy/moon/Langrenus-Labels.jpg'
              class='MagicZoomPlus' rel='zoom-position: inner'><img
              src='http://rest.canfield.com/image-zoom/images/astronomy/moon/Langrenus-Labels.thumb.jpg'/></a></div>
      <div class='crg-zoom-cell'><a href='http://rest.canfield.com/image-zoom/images/astronomy/moon/moon-ptolemaeus.jpg'
                                    class='MagicZoomPlus' rel='zoom-position: inner'><img
              src='http://rest.canfield.com/image-zoom/images/astronomy/moon/moon-ptolemaeus.thumb.jpg'/></a></div>
      <div class='crg-zoom-cell'><a
              href='http://rest.canfield.com/image-zoom/images/astronomy/moon/moon-stofler-zoom-2-wavelet.jpg'
              class='MagicZoomPlus' rel='zoom-position: inner'><img
              src='http://rest.canfield.com/image-zoom/images/astronomy/moon/moon-stofler-zoom-2-wavelet.thumb.jpg'/></a>
      </div>
    </div>
    <div class='crg-zoom-row'>
      <div class='crg-zoom-cell'><a href='http://rest.canfield.com/image-zoom/images/astronomy/moon/moon-stofler.jpg'
                                    class='MagicZoomPlus' rel='zoom-position: inner'><img
              src='http://rest.canfield.com/image-zoom/images/astronomy/moon/moon-stofler.thumb.jpg'/></a></div>
      <div class='crg-zoom-cell'><a
              href='http://rest.canfield.com/image-zoom/images/astronomy/moon/mosaic-flipped-Labels.jpg'
              class='MagicZoomPlus' rel='zoom-position: inner'><img
              src='http://rest.canfield.com/image-zoom/images/astronomy/moon/mosaic-flipped-Labels.thumb.jpg'/></a>
      </div>
    </div>
  </div>
</div>
```

## Moon Dynamic (Deprecated)

```html
<div id='crg-moon'>Moon images</div>
<script>
var CRG = CRG || {};
if (! CRG.gallery_load++ ) {CRG.gallery_load = 1;} // Handle undefined

jQuery.ajax({
    url: 'http://rest.canfield.com/image-zoom/zoom.px/astronomy/moon?callback=?',
    type: 'GET',
    dataType: 'jsonp',
    jsonpCallback: 'crgMoon',
    contentType: 'application/json',
    success: function(crg) {
      jQuery('#crg-moon').html(crg.data.html);
    },
    complete: function () {
      // Prevent multiple refreshes from triggering at once
      if (--CRG.gallery_load == 0) {
        MagicZoomPlus.refresh();
      }
    },
});
</script>
```


# Planet files

## Planets Static 

- Fetch from live site (before it was deactivated)
    - `curl http://rest.canfield.com/image-zoom/zoom.px/astronomy/planets > plantes.json`
- Filter out just the HTML
    - `jq .data.html moon.json`
- Used IntelliJ to format
    - Tip: IntelliJ editor reformts works fine within Markdown fence with html tag.


```html
<div id='crg-planets'>
    <style> .crg-zoom-cell {
        display: inline;
        padding: 5px;
    }
    </style>
    <div class='crg-zoom'>
        <div class='crg-zoom-row'>
            <div class='crg-zoom-cell'><a
                    href='http://rest.canfield.com/image-zoom/images/astronomy/planets/jupiter-002-wavelet.jpg'
                    class='MagicZoomPlus' rel='zoom-position: inner'><img
                    src='http://rest.canfield.com/image-zoom/images/astronomy/planets/jupiter-002-wavelet.thumb.jpg'/></a>
            </div>
            <div class='crg-zoom-cell'><a
                    href='http://rest.canfield.com/image-zoom/images/astronomy/planets/jupiter-3-quick-reigistax-sm.jpg'
                    class='MagicZoomPlus' rel='zoom-position: inner'><img
                    src='http://rest.canfield.com/image-zoom/images/astronomy/planets/jupiter-3-quick-reigistax-sm.thumb.jpg'/></a>
            </div>
            <div class='crg-zoom-cell'><a
                    href='http://rest.canfield.com/image-zoom/images/astronomy/planets/jupiter-3-quick-reigistax.jpg'
                    class='MagicZoomPlus' rel='zoom-position: inner'><img
                    src='http://rest.canfield.com/image-zoom/images/astronomy/planets/jupiter-3-quick-reigistax.thumb.jpg'/></a>
            </div>
            <div class='crg-zoom-cell'><a
                    href='http://rest.canfield.com/image-zoom/images/astronomy/planets/jupiter-spot-high-gama-raw.jpg'
                    class='MagicZoomPlus' rel='zoom-position: inner'><img
                    src='http://rest.canfield.com/image-zoom/images/astronomy/planets/jupiter-spot-high-gama-raw.thumb.jpg'/></a>
            </div>
        </div>
        <div class='crg-zoom-row'>
            <div class='crg-zoom-cell'><a
                    href='http://rest.canfield.com/image-zoom/images/astronomy/planets/jupiter-spot-high-gama-wavelet.jpg'
                    class='MagicZoomPlus' rel='zoom-position: inner'><img
                    src='http://rest.canfield.com/image-zoom/images/astronomy/planets/jupiter-spot-high-gama-wavelet.thumb.jpg'/></a>
            </div>
            <div class='crg-zoom-cell'><a
                    href='http://rest.canfield.com/image-zoom/images/astronomy/planets/mars-camcorder.jpg'
                    class='MagicZoomPlus' rel='zoom-position: inner'><img
                    src='http://rest.canfield.com/image-zoom/images/astronomy/planets/mars-camcorder.thumb.jpg'/></a>
            </div>
            <div class='crg-zoom-cell'><a
                    href='http://rest.canfield.com/image-zoom/images/astronomy/planets/saturn-medium-gama-wavelet-sm.jpg'
                    class='MagicZoomPlus' rel='zoom-position: inner'><img
                    src='http://rest.canfield.com/image-zoom/images/astronomy/planets/saturn-medium-gama-wavelet-sm.thumb.jpg'/></a>
            </div>
            <div class='crg-zoom-cell'><a
                    href='http://rest.canfield.com/image-zoom/images/astronomy/planets/saturn-medium-gama-wavelet.jpg'
                    class='MagicZoomPlus' rel='zoom-position: inner'><img
                    src='http://rest.canfield.com/image-zoom/images/astronomy/planets/saturn-medium-gama-wavelet.thumb.jpg'/></a>
            </div>
        </div>
    </div>
</div>
```

## Planets Dynamic (Deprecated)

This is the dynamic version using Perl script. Will be recreated someday OR will use some other
service.

```html
<div id='crg-planets'>Planet images</div>
<script>
var CRG = CRG || {};
if (! CRG.gallery_load++ ) {CRG.gallery_load = 1;} // Handle undefined

jQuery.ajax({
    url: 'http://rest.canfield.com/image-zoom/zoom.px/astronomy/planets?callback=?',
    type: 'GET',
    dataType: 'jsonp',
    jsonpCallback: 'crgPlanets',
    contentType: 'application/json',
    success: function(crg) {
      jQuery('#crg-planets').html(crg.data.html);
      MagicZoomPlus.stop();
    },
    complete: function () {
      // Prevent multiple refreshes from triggering at once
      if (--CRG.gallery_load == 0) {
        MagicZoomPlus.refresh();
      }
    },
});
</script></div>
```