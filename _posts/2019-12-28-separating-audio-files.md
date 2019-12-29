---
layout: post
title:  "separating the beat from the lyrics using Deezer's spleeter"
---

# Small full stack app to separate vocals and accompaniment using Deezer's spleeter.

In this post I'll be introducing `separate-stems`, a full stack app that separates vocals and accompaniment given an audio file. The app is delivered in a docker image and it uses HTML, JavaScript, Python and Deezer's ![spleeter](https://deezer.io/releasing-spleeter-deezer-r-d-source-separation-engine-2b88985e797e) SOTA algorithm to perform the separation.

To see how to use it check [https://github.com/theccalderon/separate-stems](https://github.com/theccalderon/separate-stems).

# Index:
1. [Motivation](#motivation)
2. [Usage](#usage)
3. [Implementation](#implementation)
4. [Audio samples](#example)
5. [Future Work](#futurework)

## Motivation<a name="motivation"></a>

"Finally, it’s worth pointing out that music mixing is a fine art and that mastering sound engineers are artists in their own rights."[^fn] That's a fragment of the post where Deezer announced their new state of the art separation algorithm and I couldn't agree more. It's usually the musicians who capture the mainstream, but behind all those tracks are also the sound engineers who make sure all the details are covered.\
With this full stack application, that uses [fastai's starter kit to deploy on Render](https://course.fast.ai/deployment_render.html), Deezer's very own [spleeter](https://github.com/deezer/spleeter) and it can be run as a Docker container, I'm attempting to becomea mastering sound engineer for at least a day (the worst one, I know :().

For readers interested in just how the run the application, please see [Usage](#usage). If you're interested in how it was implemented (yessssss), please refer to the [Implementation](#implementation) section.

## Usage <a name="usage"></a>

In order to run this image you must be running the Docker daemon.

1. `git clone https://github.com/theccalderon/separate-stems.git`
2. `cd separate-stems`
3. `docker build -t separate-stems . && docker run --rm -it -p 5000:5000 separate-stems`
 
Let's focus on step number 3 here. After cloning the repository and navigating to it, we run the `docker build` command which builds the docker image from the Dockerfile. The most important detail in this Dockerfile is the following line: \
`ADD ffmpeg-git-20191121-amd64-static/ /usr/local/bin`\
and it's there because the base image we are using (python official docker hub image) does not have ffmpeg, ffprobe, both needed to work with audio files. I found the static builds for them in [https://www.johnvansickle.com/ffmpeg/](https://www.johnvansickle.com/ffmpeg/). See their FAQ for more information.\
After building the image, we execute `docker run --rm -it -p 5000:5000 separate-stems` to run the image as a container and expose port 5000 for external traffic (our application is run using [uvicorn](https://www.uvicorn.org/) and will run on port 5000, more details in the Implementation section).

## Implementation <a name="implementation"></a>

As I pointed out earlier, this application uses [https://course.fast.ai/deployment_render.html](https://course.fast.ai/deployment_render.html) as a starting point, so I will only explain the customizations for this particular project.

### client.js

This file takes care of sending the audio file the user will pick from her/his computer to the endpoint that executes the separation using spleeter. The main functions are analyze_vocals() and analyze_accompaniments() and they are very similar so let's discuss them.

```javascript
function analyze_vocals() {
  var uploadFiles = el("file-input").files;
  // sanity check
  if (uploadFiles.length !== 1) 
  {
    alert("Please select a file to analyze!");
    return;
  }

  el("analyze-button-vocals").innerHTML = "Separating...";
  // using XMLHttpRequest to send the post request
  var xhr = new XMLHttpRequest();
  var loc = window.location;
  xhr.open("POST", `${loc.protocol}//${loc.hostname}:${loc.port}/separate_vocals`,
    true);
  // setting the headers as audio/wav to send the file
  xhr.setRequestHeader('Accept', 'audio/wav');
  xhr.responseType = 'blob';
  // adding the event handlers, will make the request towards the end of thsi function.
  xhr.onerror = function() {
    alert(xhr.responseText);
  };
  // working with a blob response
  xhr.onload = function(e) {
    if (this.readyState == 4) 
    {
      try {
        // initializing blob with the response from the endpoint
        var blob = e.target.response;
        var contentDispo = this.getResponseHeader('Content-Disposition');
        console.log(contentDispo);
        // using the contentDisposition header to get the name of the file being sent as a response.
        var fileName = contentDispo.match(/filename[^;=\n]*=((['"]).*?\2|[^;\n]*)/)[1];
        console.log(fileName);
        // saveBlob function downloads the response as an audio file and saves it in your computer.
        saveBlob(blob, fileName);
      }
      catch (error)
      {
        console.log(error.message);
      }
    }
    el("analyze-button-vocals").innerHTML = "Separate vocals";
  };

  // sending the file as form data.
  var fileData = new FormData();
  fileData.append("file", uploadFiles[0]);
  xhr.send(fileData);
}
```

I tried commenting the code however, the interesting part here is how to use `XMLHttpRequest` to send an audio file to and endpoint and deal with a binary file as a response.

Now, off to the separation.

### server.py

This file has the code to create and run the server. The main functions are separate_accompaniament and separate_vocals. Once again very similar to each other:

```python
path = Path(__file__).parent
from spleeter.separator import Separator

@app.route('/separate_vocals', methods=['POST'])
async def separate_vocals(request):
    # getting the file sent by client.js
    audio_data = await request.form()
    audio_bytes = await (audio_data['file'].read())
    # making a temporary file
    temp, pathname = tempfile.mkstemp()
    #as the spleeter library only provides one function to separate both the vocals and accompaniment, we need to check if the same file has been submitted before in order to save time and just grab the existing file already.
    if (os.path.exists('/tmp/salsita/'+str(pathname).split('/')[2])):
        response = FileResponse('/tmp/salsita/'+str(pathname).split('/')[2]+'/vocals.wav', media_type = 'audio/wav',filename='vocals.wav')
        return response    
    with open(pathname, mode='wb') as f:
        f.write(audio_bytes)
    # using the two stems separator, spleeter offers more so go check it out!
    separator = Separator('spleeter:2stems')
    separator.separate_to_file(pathname, "/tmp/salsita")
    response = FileResponse('/tmp/salsita/'+str(pathname).split('/')[2]+'/vocals.wav', media_type = 'audio/wav',filename='vocals.wav')
    return response
```

What's interesting here???? Well, this functions creates a binary file from the form data sent from client.js, executes the SOTA algorithm provided by the `spleeter` library and returns the new audio file to client.js

### Other things to point out.

I thought about using a cron job here as well to demonstrate how to run scheduled jobs inside a container. The cron job makes sure to delete the temp files every day ;).

`0 23 * * * find /tmp -type f -atime +1 -delete`

## Audio Samples <a name="example"></a>

I used this app to separate **Se me olvido que te olvide** by **Diego El Cigala** and **Bebo Valdes** from **Lagrimas Negras**, arguably the best Spanish sang album of the last 20 years. Check both files below, Bebo was a musical genius. 

![Vocals](https://separate-stems.s3.amazonaws.com/vocals.wav)
![Accompaniment](https://separate-stems.s3.amazonaws.com/vocals.wav)

## Future work <a name="futurework"></a>

It'll be super interesting to see what can we do with tools like this. One might think of using the vocals only and submit them to a transcription service like Amazon Transcribe or Google's equivalent and voila, autogenerated lyrics???? But we need to be very careful with copyright statements.

### References
[^fn]: ![Releasing Spleeter: Deezer Research source separation engine](https://deezer.io/releasing-spleeter-deezer-r-d-source-separation-engine-2b88985e797e)