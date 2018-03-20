# Renderer Example<a name="examples-renderer"></a>

The [Stream Parser Library](parser-library.md) contains a demo application named `KinesisVideoRendererExample` that demonstrates parsing and rendering Amazon Kinesis video stream fragments\. The example uses [JCodec](http://jcodec.org/) to decode the H\.264 encoded frames that are ingested using the [GStreamer Example](examples-gstreamer.md) application\. After the frame is decoded using JCodec, the visible image is rendered using [JFrame](https://docs.oracle.com/javase/7/docs/api/javax/swing/JFrame.html)\. 

This example shows how to do the following:
+ Retrieve frames from a Kinesis video stream using the `GetMedia` API and render the stream for viewing\.
+ View the video content of streams in a custom application instead of using the Kinesis Video Streams console\.

You can also use the classes in this example to view Kinesis video stream content that isn't encoded as H\.264, such as a stream of JPEG files that don't require decoding before being displayed\.

The following procedure demonstrates how to set up and use the Renderer demo application\.

## Prerequisites<a name="examples-renderer-prerequisites"></a>

To examine and use the Renderer example library, you must have the following:
+ An Amazon Web Services \(AWS\) account\. If you don't already have an AWS account, do the following:

  1. Open [https://aws\.amazon\.com/](https://aws.amazon.com/), and then choose **Create an AWS Account**\.
**Note**  
This option might be unavailable in your browser if you previously signed in to the AWS Management Console\. In that case, choose **Sign In to the Console**, and then choose **Create a new AWS account**\.

  1. Follow the online instructions\.

     Part of the sign\-up procedure involves receiving a phone call and entering a PIN using the phone keypad\.

     Note your AWS account ID because you need it for configuring programmatic access to Kinesis video streams\.
+ A Java integrated development environment \(IDE\), such as [Eclipse Java Neon](http://www.eclipse.org/downloads/packages/eclipse-ide-java-and-dsl-developers/neon3) or [JetBrains IntelliJ Idea](https://www.jetbrains.com/idea/download/)\.

## Running the Renderer Example<a name="examples-renderer-procedure"></a>

1. Create a directory, and then clone the example source code from the GitHub repository\.

   ```
   $ git clone https://github.com/aws/amazon-kinesis-video-streams-parser-library
   ```

1. Open the Java IDE that you are using \(for example, [Eclipse](http://www.eclipse.org/) or [IntelliJ IDEA](https://www.jetbrains.com/idea/)\) and import the Apache Maven project that you downloaded: 
   + **In Eclipse:** Choose **File**, **Import**, **Maven**, **Existing Maven Projects**\. Navigate to the `kinesis-video-streams-parser-lib` directory\.
   + **In IntelliJ Idea: ** Choose **Import**\. Navigate to the `pom.xml` file in the root of the downloaded package\.

    For more information, see the related IDE documentation\.

1. From your Java IDE, open `src/test/java/com.amazonaws.kinesisvideo.parser/examples/KinesisVideoRendererExampleTest`\. 

1. Remove the `@Ignore` directive from the file\.

1. Update the `.stream` parameter with the name of your Kinesis video stream\.

1. Run the `KinesisVideoRendererExample` test\.

## How It Works<a name="examples-renderer-howitworks"></a>

**Topics**
+ [Sending MKV data](#examples-renderer-howitworks-send)
+ [Parsing MKV Fragments into Frames](#examples-renderer-howitworks-parse)
+ [Decoding and Displaying the Frame](#examples-renderer-howitworks-display)

### Sending MKV data<a name="examples-renderer-howitworks-send"></a>

The example sends sample MKV data from the `rendering_example_video.mkv` file, using PutMedia to send video data to a stream named **render\-example\-stream**\.

The application creates a `PutMediaWorker`:

```
PutMediaWorker putMediaWorker = PutMediaWorker.create(getRegion(),
    getCredentialsProvider(),
    getStreamName(),
    inputStream,
    streamOps.amazonKinesisVideo);
executorService.submit(putMediaWorker);
```

For information about the `PutMediaWorker` class, see [Call PutMedia](parser-library-write.md#parser-library-write-example-putmedia) in the [Stream Parser Library](parser-library.md) documentation\.

### Parsing MKV Fragments into Frames<a name="examples-renderer-howitworks-parse"></a>

The example then retrieves and parses the MKV fragments from the stream using a `GetMediaWorker`:

```
GetMediaWorker getMediaWorker = GetMediaWorker.create(getRegion(),
    getCredentialsProvider(),
    getStreamName(),
    new StartSelector().withStartSelectorType(StartSelectorType.EARLIEST),
    streamOps.amazonKinesisVideo,
    getMediaProcessingArgumentsLocal.getFrameVisitor());
executorService.submit(getMediaWorker);
```

For more information about the `GetMediaWorker` class, see [Call GetMedia](parser-library-write.md#parser-library-write-example-getmedia) in the [Stream Parser Library](parser-library.md) documentation\.

### Decoding and Displaying the Frame<a name="examples-renderer-howitworks-display"></a>

The example then decodes and displays the frame using [JFrame](https://docs.oracle.com/javase/7/docs/api/javax/swing/JFrame.html)\.

The following code example is from the `KinesisVideoFrameViewer` class, which extends `JFrame`:

```
 public void setImage(BufferedImage bufferedImage) {
    image = bufferedImage;
    repaint();
}
```