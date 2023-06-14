### Twig Development Cheatsheet
---
### Getting Media

Obtaining media data can sometimes be tricky. There are several factors that determine how the variables are found including if the media has an internal or external path, a relative or absolute path, if the media is an image or video, if the media comes from a single field or WYSIWYG, and more. Below are a few successful options used with getting media data and displaying it to front-end twig templates.

<p>&nbsp;</p>

**Internal single image media field (url and alt text)**

```php
  content.field_image[0]['#media'].field_media_image.entity.uri.value
  content.field_image[0]['#media'].field_media_image.alt
```
The associated field variable name above is the Drupal default “field_image”, however depending on how the field is created in the Drupal administration website, the variable name will reflect that particular field name. For example, if we create a field for a media library reference for an image called Featured Image, the associated field variable name will most likely be “field_featured_image”.

<p>&nbsp;</p>

**Displaying a media asset in HTML (clean method - default image style)**

```php
{% set image = {
  '#theme':       'image_style',
  '#style_name':  '16_9_960x540',
  '#uri':         content.field_image[0]['#media'].field_media_image.entity.uri.value,
  '#alt':         content.field_image[0]['#media'].field_media_image.alt,
  '#height':      optional,
  '#width':       optional
} %}

<div class="container">
   {{ image }}
</div>
```
This will allow Drupal to access a render array and dynamically display the image with all Drupal image attributes intact. Providing the style name (must be the machine name) will apply any [image style](https://www.drupal.org/docs/user_guide/en/structure-image-style-create.html) that has been defined in the Drupal media configuration settings. This includes using any conversion extensions in the image style pipeline, for example, converting an image to WebP.

<p>&nbsp;</p>

**Displaying a media asset in HTML (clean method - responsive image style)**

```php
{% set image = {
  '#theme':                      'responsive_image',
  '#responsive_image_style_id':  'card_16_9',
  '#uri':                        content.field_image[0]['#media'].field_media_image.entity.uri.value,
  '#alt':                        content.field_image[0]['#media'].field_media_image.alt,
  '#height':                     optional,
  '#width':                      optional
} %}

<div class="container">
   {{ image }}
</div>
```
Getting access to and displaying the image when using Drupal's [responsive image styles](https://www.drupal.org/docs/user_guide/en/structure-image-responsive.html) will fully render a picture tag with the necessary source sets. Conversion extensions should still work with this method, in fact WebP will render both the WebP and fallback image formats within the picture source set tags.

<p>&nbsp;</p>

**Displaying a media asset in HTML (dirty method)**

```
file_url(content.field_image[0]['#media'].field_media_image.entity.uri.value)
```
The “uri.value” will simply get the path object, using “file_url” will convert the path object into a usable url to add within an image HTML element.

```
{% set imageUrl = file_url(content.field_image[0]['#media'].field_media_image.entity.uri.value) %}
{% set imageAlt = content.field_image[0]['#media'].field_media_image.alt %}

<img src="{{ imageUrl }}" alt="{{ imageAlt }}">
```

<p>&nbsp;</p>


**Debugging and acessing Twig variables**

```
<script>console.log({{ _context | json_encode | raw}});</script>

{{ dump() }}
```
