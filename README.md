# Laravel-Vue-QUploader
fast and easiest way to upload files in [Laravel](https://laravel.com/) [Vue](https://vuejs.org/) app using [quasar-framework](https://quasar.dev/)  QUploader

Create [Vue](https://vuejs.org/) component 
```[vue]

<template>
    <div class=" row my-card">
      <q-uploader
        class="my-card"
        accept="image/*"
        :fieldName="(file) =>`image${file.name}`"
        :headers="[ { name: 'Authorization', value: $toks }]"
        with-credentials
        multiple
        batch
        @uploaded="useUploaded"
        :url="files=>`${$url}/upload-image`"
        label="Select Course Image to Upload"

      />
    </div>
</template>

<script lang="ts">
import {
  defineComponent
} from '@vue/composition-api'

export default defineComponent({
  name: 'ImageUploader',
  setup () {
    function useUploaded (info:{files:string[];xhr:{response:string}}) {
      console.log('files :>> ', info.files)
      console.log('url :>> ', JSON.parse(info.xhr.response))
    }
    return { useUploaded }
  }
})
</script>
```
[Laravel](https://laravel.com/) route in my case i will API
```[php]

Route::post('upload-image', 'ImageController@ImageUpload');

```

[Laravel](https://laravel.com/) controller 

```[php]

<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Storage;
use Validator;

class ImageController extends Controller
{
    public function __construct()
    {
        $this->middleware(['auth:api'])->except([]);
    }

    public function ImageUpload(Request $r)
    {
        $validator = Validator::make($r->all(), [
            'image.*' => 'required|file|image|mimes:jpeg,png,gif,svg'
        ]);
        if ($validator->fails()) {
            logger(['error' => $validator->errors()]);
            return ['status' => 0];
        }
        $images = $r->all();
        $paths = [];
        foreach ($images as $image) {
            $extension = $image->getClientOriginalName();
            $filename  = 'image-' . time() . '.' . $extension;
            Storage::disk('public')->putFileAs('/images/', $image, $filename);
            $paths[] =   url(Storage::url('images/'.$filename));
        }

        return $paths;
    }
}


```

if any issue [check](https://github.com/diadal/laravel-vue-q-uploader/issues)

*also you can buy me a coffee @ [Patreon](https://www.patreon.com/diadal)*
