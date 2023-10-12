# 3dviewer-API
3D Viewer online (https://www.3dvieweronline.com) is an online collaboration platform for 3D models that allows you to view, publish and collaborate on 3D models.

The current REST API allows to list, upload, delete 3D models and more . To work with our API you will need an Api Token, which you can request by using the support form at www.3dvieweronline.com (please select support as a subject), specifying your user email.

The REST API are subject to a fair rate limit, please contact us to have more information.

# Supported file formats
Import: obj, zip (obj with mtl and texture), stl, ply, step, iges, dxf, dwg.

# First API request

Before uploading or deleting models, you should check your API endpoint connection and set up your response handler with a simple GET request:

```
GET https://www.3dvieweronline.com/api/v1/models/?token={your-api-token}
```
CURL example:
```
curl "https://www.3dvieweronline.com/api/v1/models/?token={your-api-token}"
```

PHP example:
```
$token = 'your-api-token';
$url = 'https://www.3dvieweronline.com/api/v1/models/?token=' . $token;

//Set up the curl connection
$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, $url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 2);
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, true);
curl_setopt($ch, CURLOPT_CAINFO, 'Absolute/path/to/cacert.pem'); //absolute path to local certification file
$result = curl_exec($ch);
$err = curl_error($ch);
curl_close($ch);
if($err){
echo 'cURL Error: ' . $err;
} else {
print_r(json_decode($result, true));
}
```

Successful response:
```
{ "success" : true,
"url" : "https://www.3dvieweronline.com/members/Id9bf31c7ff062936a96d3c8bd1f8f2ff3/3VCKJVxOIM45tzr",
"modelID" : 1234 }
```

Response with errors:
```
{ "success" : false,
"error" : "Error message" }
```

When you are establishing a SSL connection with curl, please be sure you have a valid CA certificate in your server or CURLOPT_SSL_VERIFYPEER will return an error. If not, you can download a mozilla certificate from here: http://curl.haxx.se/docs/caextract.html.

Even though not advisable for security reasons, you can disable all SSL checks using the following options:

```
curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 0);
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
// no need to specify CURLOPT_CAINFO
```

# Model details

You can use the GET API request to retrieve the details of a specific model or the details of all the models uploaded to your account. Please use a specific modelID (e.g. modelID=1234) to retrieve the details about a specific model, or set modelID to 0 (i.e. modelID=0) to retrieve the details of all the models uploaded to your account.

```
GET https://www.3dvieweronline.com/api/v1/models/?token={your-api-token}&modelID={model-ID}
```

CURL example
```
curl "https://www.3dvieweronline.com/api/v1/models/?token={your-api-token}&modelID={model-ID}"
```

PHP example
```
$token = 'your-api-token';
$modelID = 'your-model-ID'; // $modelID = '0' to list all the models
$url = 'https://www.3dvieweronline.com/api/v1/models/?token=' . $token .'&modelID=' . $modelID;

//Set up the curl connection
$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, $url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 2);
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, true);
curl_setopt($ch, CURLOPT_CAINFO, 'Absolute/path/to/cacert.pem'); //absolute path to local certification file
$result = curl_exec($ch);
$err = curl_error($ch);
curl_close($ch);
if($err){
echo 'cURL Error: ' . $err;
} else {
print_r(json_decode($result, true));
}
```

Successful response (multiple results)
```
{
"success": true,
"message": "Message",
"results": {
"modelID": [
1234,
1235
],
"url": [ //url of the 3DViewer page
"https://...",
"https://..."
],
"thumbnail": [ //url of the thumbnail
"https://.../thumbnail1.png",
"https://.../thumbnail2.png"
],
"tags": [
["tag1","tag2"],
["tag3"]
],
"modelName": [
"filename1.stp",
"filename2.stp"
],
"modelSize": [ //in Bytes
9337,
38871
],
"date": [
"YYYY-MM-DD hh:mm:ss",
"YYYY-MM-DD hh:mm:ss"
],
"numResults": 2, //number of models
"totSize": 48208 //total size in Bytes
}
}
```

Response with errors
```
{ "success" : false,
"error" : "Error message" }
```

# Upload a model

Uploading a model requires a POST API request:

CURL example uploading a local file
```
curl -F userFile="@/path/to/your-model.stp" "https://www.3dvieweronline.com/api/v1/models/?token={your-api-token}"
```

CURL example uploading a file from URL
```
curl -F fileUrl="http://yoursite.com/your-model.stp" -F filename="your-model.stp" "https://www.3dvieweronline.com/api/v1/models/?token={your-api-token}"
```
Notes: fileUrl must be passed url encoded to include special characters. If no filename is passed, the system will try to retrieve it from the URL. For complex URL however, this might fail generating errors, so the use of the filename parameter is advised.

PHP example uploading a local file
```
$token = 'your-api-token';
$url = 'https://www.3dvieweronline.com/api/v1/models/?token=' . $token;
$cFile = curl_file_create(realpath('./local/path/to/your-model.stp'));
$post = array(
"userFile" => $cFile
);
//Set up the curl connection
$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, $url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 2);
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, true);
curl_setopt($ch, CURLOPT_CAINFO, 'Absolute/path/to/cacert.pem'); //absolute path to local certification file
curl_setopt($ch, CURLOPT_POST,true);
curl_setopt($ch, CURLOPT_POSTFIELDS, $post);
$result = curl_exec($ch);
$err = curl_error($ch);
curl_close($ch);
if($err){
echo 'cURL Error: ' . $err;
} else {
print_r(json_decode($result, true));
}
```

PHP example uploading a a file from URL
```
$token = 'your-api-token';
$url = 'https://www.3dvieweronline.com/api/v1/models/?token=' . $token;
$fileUrl = 'http://yoursite.com/your-model.stp';
$filename = 'your-model.stp';
$post = array(
"fileUrl" => $fileUrl,
"filename" => $filename);
//Set up the curl connection
$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, $url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 2);
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, true);
curl_setopt($ch, CURLOPT_CAINFO, 'Absolute/path/to/cacert.pem'); //absolute path to local certification file
curl_setopt($ch, CURLOPT_POST,true);
curl_setopt($ch, CURLOPT_POSTFIELDS, $post);
$result = curl_exec($ch);
$err = curl_error($ch);
curl_close($ch);
if($err){
echo 'cURL Error: ' . $err;
} else {
print_r(json_decode($result, true));
}
```

Notes: fileUrl must be passed url encoded to include special characters.

Successful response
```
{ "success" : true,
"url" : "https://www.3dvieweronline.com/members/Id9bf31c7ff062936a96d3c8bd1f8f2ff3/3VCKJVxOIM45tzr",
"modelID" : 1234 , "message": "message", "thumbnail":"https://location/of/your/thumbnail.png"}
```

Response with errors
```
{ "success" : false,
"error" : "Error message" }
```

# Upload a model (retrieve from account)

To codevent that *identical models are uploaded multiple times into a user account, the retrievemodel parameter can be used. When retrievemodel is set to true, the system automatically checks if the model to upload already exists into the user account. If the model is found, the system then retrieves its details and returns them in the API answer. This allows to improve the performance and reduce the account storage.

*Two models are considered identical if they have the same filename and the same filesize.

CURL example uploading a local file
```
curl -F userFile="@/path/to/your-model.stp" -F retrievemodel="true" "https://www.3dvieweronline.com/api/v1/models/?token={your-api-token}"
```

CURL example uploading a file from URL
```
curl -F fileUrl="http://yoursite.com/your-model.stp" -F filename="your-model.stp" -F retrievemodel="true" "https://www.3dvieweronline.com/api/v1/models/?token={your-api-token}"
```
Notes: fileUrl must be passed url encoded to include special characters. If no filename is passed, the system will try to retrieve it from the URL. For complex URL however, this might fail generating errors, so the use of the filename parameter is advised.

PHP example uploading a local file
```
$token = 'your-api-token';
$url = 'https://www.3dvieweronline.com/api/v1/models/?token=' . $token;
$cFile = curl_file_create(realpath('./local/path/to/your-model.stp'));
$post = array(
"userFile" => $cFile,
"retrievemodel" => "true"
);
//Set up the curl connection
$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, $url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 2);
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, true);
curl_setopt($ch, CURLOPT_CAINFO, 'Absolute/path/to/cacert.pem'); //absolute path to local certification file
curl_setopt($ch, CURLOPT_POST,true);
curl_setopt($ch, CURLOPT_POSTFIELDS, $post);
$result = curl_exec($ch);
$err = curl_error($ch);
curl_close($ch);
if($err){
echo 'cURL Error: ' . $err;
} else {
print_r(json_decode($result, true));
}
```

PHP example uploading a a file from URL
```
$token = 'your-api-token';
$url = 'https://www.3dvieweronline.com/api/v1/models/?token=' . $token;
$fileUrl = 'http://yoursite.com/your-model.stp';
$filename = 'your-model.stp';
$post = array(
"fileUrl" => $fileUrl,
"filename" => $filename,
"retrievemodel" => "true"
);
//Set up the curl connection
$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, $url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 2);
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, true);
curl_setopt($ch, CURLOPT_CAINFO, 'Absolute/path/to/cacert.pem'); //absolute path to local certification file
curl_setopt($ch, CURLOPT_POST,true);
curl_setopt($ch, CURLOPT_POSTFIELDS, $post);
$result = curl_exec($ch);
$err = curl_error($ch);
curl_close($ch);
if($err){
echo 'cURL Error: ' . $err;
} else {
print_r(json_decode($result, true));
}
```

Notes: fileUrl must be passed url encoded to include special characters.

Successful response
```
{ "success" : true,
"url" : "https://www.3dvieweronline.com/members/Id9bf31c7ff062936a96d3c8bd1f8f2ff3/3VCKJVxOIM45tzr",
"modelID" : 1234 , "message": "message", "thumbnail":"https://location/of/your/thumbnail.png"}
```

Response with errors
```
{ "success" : false,
"error" : "Error message" }
```

# Update a model

Updating a model already uploaded, requires a POST API request with the updatemodel parameter set to true:

CURL example updating a model with a local file
```
curl -F userFile="@/path/to/your-model.stp" -F updatemodel="true" "https://www.3dvieweronline.com/api/v1/models/?token={your-api-token}"
```

CURL example updating a model with a file from URL
```
curl -F fileUrl="http://yoursite.com/your-model.stp" -F filename="your-model.stp" -F updatemodel="true" "https://www.3dvieweronline.com/api/v1/models/?token={your-api-token}"
```
Notes: fileUrl must be passed url encoded to include special characters. If no filename is passed, the system will try to retrieve it from the URL. For complex URL however, this might fail generating errors, so the use of the filename parameter is advised.

PHP example uploading a local file
```
$token = 'your-api-token';
$url = 'https://www.3dvieweronline.com/api/v1/models/?token=' . $token;
$cFile = curl_file_create(realpath('./local/path/to/your-model.stp'));
$post = array(
"userFile" => $cFile,
"updatemodel" => "true"
);
//Set up the curl connection
$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, $url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 2);
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, true);
curl_setopt($ch, CURLOPT_CAINFO, 'Absolute/path/to/cacert.pem'); //absolute path to local certification file
curl_setopt($ch, CURLOPT_POST,true);
curl_setopt($ch, CURLOPT_POSTFIELDS, $post);
$result = curl_exec($ch);
$err = curl_error($ch);
curl_close($ch);
if($err){
echo 'cURL Error: ' . $err;
} else {
print_r(json_decode($result, true));
}
```

PHP example uploading a a file from URL
```
$token = 'your-api-token';
$url = 'https://www.3dvieweronline.com/api/v1/models/?token=' . $token;
$fileUrl = 'http://yoursite.com/your-model.stp';
$filename = 'your-model.stp';
$post = array(
"fileUrl" => $fileUrl,
"filename" => $filename,
"updatemodel" => "true"
);
//Set up the curl connection
$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, $url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 2);
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, true);
curl_setopt($ch, CURLOPT_CAINFO, 'Absolute/path/to/cacert.pem'); //absolute path to local certification file
curl_setopt($ch, CURLOPT_POST,true);
curl_setopt($ch, CURLOPT_POSTFIELDS, $post);
$result = curl_exec($ch);
$err = curl_error($ch);
curl_close($ch);
if($err){
echo 'cURL Error: ' . $err;
} else {
print_r(json_decode($result, true));
}
```

Notes: fileUrl must be passed url encoded to include special characters.

Successful response
```
{ "success" : true,
"url" : "https://www.3dvieweronline.com/members/Id9bf31c7ff062936a96d3c8bd1f8f2ff3/3VCKJVxOIM45tzr",
"modelID" : 1234 , "message": "message", "thumbnail":"https://location/of/your/thumbnail.png"}
```

Response with errors
```
{ "success" : false,
"error" : "Error message" }
```

# Delete a model

You can delete a model with a DELETE API request knowing the modelID or by using the age parameter.

Deleting a model with the modelID:

CURL example
```
curl -X DELETE "https://www.3dvieweronline.com/api/v1/models/?token={your-api-token}&modelID={model-ID}"
```

PHP example deleting a model
```
$token = 'your-api-token';
$modelID = 'your-model-ID';
$url = 'https://www.3dvieweronline.com/api/v1/models/?token=' . $token . '&modelID=' . $modelID;


//Set up the curl connection
$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, $url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 2);
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, true);
curl_setopt($ch, CURLOPT_CAINFO, 'Absolute/path/to/cacert.pem'); //absolute path to local certification file
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, 'DELETE');
$result = curl_exec($ch);
$err = curl_error($ch);
curl_close($ch);
if($err){
echo 'cURL Error: ' . $err;
} else {
print_r(json_decode($result, true));
}
```

Successful response
```
{ "success" : true,
"message" : "Model 1234 deleted"}
```

Response with errors
```
{ "success" : false,
"error" : "Error message" }
```

Deleting a model with the age parameter:

The age parameter allows to delete all the models older than the specified age in days. If age is 30, all the models older than 30 days will be deleted. Please note that if age is set to 0, all the models will be deleted. If the number of models to delete is higher than the DELETE API call hourly limit, the system will automatically stop once the limit has been reached.

CURL example
```
curl -X DELETE "https://www.3dvieweronline.com/api/v1/models/?token={your-api-token}&age={age-in-days}"
```

PHP example deleting a model
```
$token = 'your-api-token';
$age = 'age-in-days'; // $age = '0' to delete all the models
$url = 'https://www.3dvieweronline.com/api/v1/models/?token=' . $token . '&age=' . $age;

//Set up the curl connection
$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, $url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 2);
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, true);
curl_setopt($ch, CURLOPT_CAINFO, 'Absolute/path/to/cacert.pem'); //absolute path to local certification file
curl_setopt($ch, CURLOPT_CUSTOMREQUEST, 'DELETE');
$result = curl_exec($ch);
$err = curl_error($ch);
curl_close($ch);
if($err){
echo 'cURL Error: ' . $err;
} else {
print_r(json_decode($result, true));
}
```

Successful response
```
{ "success" : true,
"message" : "Message",
"error" : []}
```

If errors occurred deleting the models, their model ID will be reported in the error array.

Response with errors
```
{ "success" : false,
"error" : "Error message" }
```

# Geometry Information

You can retrieve the geometry information of a specific model by using the geometry end-point. This will return the model area, volume, overall dimensions along the 3 axis (xbox, ybox, zbox) and the number of parts if the model is a 3D assembly. Please use a specific modelID (e.g. modelID=1234) to specify the model you want to retrieve the geometry information from.
```
GET https://www.3dvieweronline.com/api/v1/geometry/?token={your-api-token}&modelID={model-ID}
```
CURL example
```
curl "https://www.3dvieweronline.com/api/v1/geometry/?token={your-api-token}&modelID={model-ID}"
```
PHP example
```
$token = 'your-api-token';
$modelID = 'your-model-ID';
$url = 'https://www.3dvieweronline.com/api/v1/geometry/?token=' . $token .'&modelID=' . $modelID;

//Set up the curl connection
$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, $url);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 2);
curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, true);
curl_setopt($ch, CURLOPT_CAINFO, 'Absolute/path/to/cacert.pem'); //absolute path to local certification file
$result = curl_exec($ch);
$err = curl_error($ch);
curl_close($ch);
if($err){
echo 'cURL Error: ' . $err;
} else {
print_r(json_decode($result, true));
}
```

Successful response
```
{
"success": true,
"message": "Message",
"results": {
"area": "170.8222",
"volume": "6.5315",
"xbox": "8.3321",
"ybox": "10.1477",
"zbox": "8.3321",
"parts": "10" }
}
```

Response with errors
```
{ "success" : false,
"error" : "Error message" }
```
