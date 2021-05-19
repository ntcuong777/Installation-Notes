# This note present how to download/upload file from/to Google Drive with terminal

## 0. Install cURL
We use cURL to perform these operations. Install cURL:
```
$ sudo apt install curl
```

## 1. Download file from Google Drive

Suppose you want to download a file, named `pytorch.zip`, at link:
- [https://drive.google.com/file/d/1iEBb6yBvGFHO4ONomkbzVE3sQmqBOmxv](https://drive.google.com/file/d/1iEBb6yBvGFHO4ONomkbzVE3sQmqBOmxv)

Here, the file-name is `pytorch.zip` and the file-id is `1iEBb6yBvGFHO4ONomkbzVE3sQmqBOmxv`

We use this command at terminal (replace the appropriate file-name and file-id in the corresponding fields):
```
$ fileid="my-file-name"
$ filename="my-file-id"

$ curl -c ./cookie -s -L \
	"https://drive.google.com/uc?export=download&id=${fileid}" > /dev/null
$ curl -Lb ./cookie \
	"https://drive.google.com/uc?export=download&confirm=`awk '/download/ {print $NF}' ./cookie`&id=${fileid}" \
	-o ${filename}
```

## 2. Upload file to Google Drive
Now, we will do uploading this file back to Google Drive

### 2.1 Set up Credentials for Google Cloud Platform (do this once only)

#### **Step a.** Go to this link:

- [https://console.developers.google.com/apis/credentials?pli=1](https://console.developers.google.com/apis/credentials?pli=1)

#### **Step b.** We select the Credentials tab (on the left) and “create credentials”
![](https://miro.medium.com/max/700/1*dN4GWsvluseqAXfsnNZSMA.png)

***Note***: You may need to initialize **OAuth consent screen** before creating credentials

#### **Step c.** We select TV and other for app type:

![](https://miro.medium.com/max/700/1*q4O5zivox7I3vvF2rMdGag.png)

- This generates a  `client id`  and a  `client secret`  .

> **Note**:  This is your **username** and **password** so copy it somewhere secure.

### 2.2 Verify the device (do this once on each of your device)
To do this we `ssh` into the machine we wish to upload from and run the following command (**Note**: replace `<client_id>` with your `client_id`):
```
$ curl -d \
	"client_id=<client_id>&scope=https://www.googleapis.com/auth/drive.file" \
	https://oauth2.googleapis.com/device/code
```
- We get a response in the following format
```
{
	"device_code": "<long string>",  
	"user_code": "xxx-xxx-xxx",  
	"expires_in": 1800,  
	"interval": 5,  
	"verification_url": "https://www.google.com/device"
}
```
- We need to visit [https://www.google.com/device](https://www.google.com/device)
	- We provide the **user code** to complete our verification. 
	- We now continue to select our google account and *grant the relevant permissions*. 
	- When doing this make sure to note down the **device code** for the next step.

### 2.3 Get Bearer code (do this once on each of your device)

When we start uploading, this is the code we shall need to use to identify our account. We get it by using the following command: 	
- replace `<client_id>` with your `client_id`
- replace `<client secret>` with your `client secret`
- replace `<device code>` with your `device code`
```
$ curl -d client_id=<client id> \
	-d client_secret=<client secret> \
	-d device_code=<device code> \
	-d grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Adevice_code \
	https://accounts.google.com/o/oauth2/token
```

The output should be in the format:
```
{  
	"access_token": ".....",  
	"expires_in": 3599,  
	"refresh_token": "....",  
	"scope": "https://www.googleapis.com/auth/drive.file",  
	"token_type": "Bearer"  
}
```


**Notice**: Now, you need to note down
- **client id**
- **client secret**
- **device code**
- **access_token**

### 2.4 Upload files
Go to [https://console.cloud.google.com/apis/api/drive.googleapis.com/overview](https://console.cloud.google.com/apis/api/drive.googleapis.com/overview), and enable the Google Drive API to allow to upload file.

The command we use to upload files is given below:
- replace `<enter access token here>` with your `access_token`
- replace two `<our.zip>` with your `file_name` you need to upload
```
$ curl -X POST -L \  
	-H "Authorization: Bearer <enter access token here>" \  
	-F "metadata={name :'<our.zip>'};type=application/json;charset=UTF-8" \  
	-F "file=@<our.zip>;type=application/zip" \  
	"https://www.googleapis.com/upload/drive/v3/files?uploadType=multipart"
```
**Note:**
- _Here you may need to enable the app API before being allowed to upload data. The link to do this is given in the error message if this is the case._

- Here  _multipart_  files are expected to only be a couple of MB in size. However if you are looking at moving larger files  _resumable_ may be better suited (see  [https://developers.google.com/drive/api/v3/manage-uploads](https://developers.google.com/drive/api/v3/manage-uploads)  )

## QUICK NOTE:

```
########################################################################
# INIT GDRIVE API ---> GET ID+SECRET AS USR+PWD
########################################################################

clientid=""
clientsecret=""

########################################################################
# GENERATE DEVICE CODE
########################################################################

curl -d \
	"client_id=${clientid}&scope=https://www.googleapis.com/auth/drive.file" \
	https://oauth2.googleapis.com/device/code

# copy user_code, verify at <https://www.google.com/device>
# note down the device_code value:

devicecode=""

########################################################################
# GENERATE ACCESS TOKEN
########################################################################
curl -d client_id=${clientid} \
	-d client_secret=${clientsecret} \
	-d device_code=${devicecode} \
	-d grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Adevice_code \
	https://accounts.google.com/o/oauth2/token

# note down the access_token value:

accesstoken=""
filename=""

curl -X POST -L \
	-H "Authorization: Bearer ${accesstoken}" \
    -F "metadata={name :'${filename}'};type=application/json;charset=UTF-8" \
    -F "file=@${filename};type=application/zip" \
    "https://www.googleapis.com/upload/drive/v3/files?uploadType=multipart"
```

