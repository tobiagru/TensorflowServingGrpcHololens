# TensorflowServingGrpcHololens
Since it I found it to be quite painful to get a gRPC client up and running for tensorflow serving on the Hololens building a UWP with Unity. I figured I record what I did to make it happen here:

## The Versions:
So I noticed there are quite drastic differences between versions of unity, c#/.net , gRPC, tensorflow & tensorflow_serving and they all develop at different development speeds. It is quite important to match versions.
I used:
* Windows 10
* Unity 18.2
    * .NET Backend 4.x
    * .NET API Compatibility 4.x (resorts to a mix of C#6 & C#7)
* .NET SDK ???
* gRPC 1.15.0
* tensorflow 1.12
* tensorflow_serving latest(11.2.19)
* protoc 3.6.1
* protobuf runtime 3.6.1

## Getting all the necessary sources:
* Unity 
    - Download from for your platform https://unity3d.com/get-unity/download
    - Install
* gRPC 
    - Search for your package on https://www.nuget.org/packages/Grpc/
    - Open the NuGet Console in your Project (e.g inside Visual Studio) and download your package (e.g Install-Package Grpc -Version 1.15.0)
    - copy the creates folders (e.g gRPC.1.15.0, gRPC.Core.1.15.0, System.Interactive.Async) to your Assets/plugins folder
* Google Prototbuf
    - Search for your package on https://www.nuget.org/packages/Google.Protobuf
    - Open the NuGet Console in your Project (e.g inside Visual Studio) and download your package (e.g Install-Package Google.Protobuf -Version 3.6.1)
    - copy the creates folders (e.g Google.protobuf.3.6.1) to your Assets/plugins folder
* Tensorflow
    - Download the source (either through git clone or as zip) from https://github.com/tensorflow/tensorflow
    - Create a new folder (e.g on your Desktop) and call it TensorFlowServingClient
    - from the tensorflow directory copy the tensorflow/core folder to TensorFlowServingClient as tensorflow/core
* Tensorflow_Serving
    - Download the source (either through git clone or as zip) from https://github.com/tensorflow/serving
    - from the serving directory copy the tensorflow_serving directory to TensorFlowServingClient as tensorflow_serving
* protoc
    - Download the sources for your platform from https://github.com/protocolbuffers/protobuf/tags
    
## Compiling the protos
* In your favorite shell add the files to your PATH with "export PATH=$PATH:/absolute/path/to/the/sources/bin" in my case this was "export PATH=$PATH:/c/Users/tg/Downloads/protoc-3.6.1-win32/bin"
* cd to the TensorFlowServingClient folder
* create a new folder called client with "mkdir client"
* Now you can either build all the protos and see if it works (in my case I got a multiple defined error) with:
    - "protoc tensorflow/core/util/*.proto --csharp_out=client/"
    - "protoc tensorflow/core/framework/*.proto --csharp_out=client/"
    - "protoc tensorflow/core/protobuf/*.proto --csharp_out=client/"
    - "protoc tensorflow/core/example/*.proto --csharp_out=client/"
    - "protoc tensorflow/core/lib/core/*.proto --csharp_out=client/"
    - "protoc tensorflow_serving/config/*.proto --csharp_out=client/"
    - "protoc tensorflow_serving/apis/*.proto --csharp_out=client/"
    - " protoc tensorflow_serving/core/*.proto --csharp_out=client/"
    - "protoc tensorflow_serving/sources/storage_path/*.proto --csharp_out=client/"
*Or Depending on what kind of client you are trying to build (classification, regression, prediction) you start with the corresponding files and than compile all the imports until it can be build properly. For Example for prediction:
    - "protoc tensorflow_serving/apis/predict.proto --csharp_out=client/"
        --> import "tensorflow/core/framework/tensor.proto"; so you need to find this proto and add it and the imports in those and so on 
        --> import "tensorflow_serving/apis/model.proto"; so you need to find this proto and add it and the imports in those and so on 
    - "protoc tensorflow_serving/apis/prediction_service.proto --csharp_out=client/"
        --> same as above for import
    - "protoc tensorflow_serving/apis/prediction_log.proto --csharp_out=client/"
        --> same as above for import
