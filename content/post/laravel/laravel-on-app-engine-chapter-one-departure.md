+++
comments = true
date = "2016-04-19"
draft = true
image = ""
menu = ""
share = true
tags = []
title = "Laravel on App Engine - Chapter One: Trouble in Paradise"
excerpt = "My bitter sweet taste experience on deploying Laravel on Google App Engine"

+++

The problem is that `data:image/png;base64,` is included in the encoded contents. This will result in invalid image data when the base64 function decodes it. Remove that data in the function before decoding the string, like so.
{{< highlight php "linenos=inline" >}}
<?php
function base64_to_jpeg($base64_string, $output_file) {
    $ifp = fopen($output_file, "wb");

    $data = explode(',', $base64_string);

    fwrite($ifp, base64_decode($data[1]));
    fclose($ifp); 

    return $output_file;
}
{{< /highlight >}}