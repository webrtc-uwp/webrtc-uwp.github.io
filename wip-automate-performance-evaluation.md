# Automate Performance Evaluation

> Note: This document is a work in progress and is captured for the benefit of knowledge transfer.

## Performance evaluation tools
  
There are several tools to analyze the performance of the system that we need to look into each one to find the best for our purpose.

- [Windows Performance Toolkit](https://docs.microsoft.com/en-us/windows-hardware/test/wpt/)
  - Windows Performance Recorder (WPR) : records any events that creates an ETW. It can be installed and used in one of the following ways:
    - User Interface called, Windows Performance Recorder
    - Command line, WPR.exe
  - Windows Performance Analyzer (WPA): which has graphical views
- [Device Portal for Hololense](https://docs.microsoft.com/en-us/windows/uwp/debug-test-perf/device-portal-hololens): This can test the performance on Hololense.
- GPU View : for checking the performance of the GPU
- [Media eXperience Analyzer](https://channel9.msdn.com/Shows/Defrag-Tools/DefragTools-149-Media-eXperience-Analyzer-part-1?term=baldner&lang-en=true): This is a graphical tool to show the performance of the system and glitches in the media. Apparently it needs to get the logs from another tool like WPR and then you can connect it to this tool to see the output graphically.

About the windows performance tools that was mentioned above, if they don't have options to run it with powershell , command line etc. we can't use it in the pipeline.

## Specific to Azure DevOps

There might be tools that can be used that are specific to Azure Dev Ops. It is work in Progress.

- The Analytics extension on Azure DevOps can help us in the performance analysis.
  - Here is the [link](https://marketplace.visualstudio.com/items?itemName=ms.vss-analytics) to the widgets that can be added to our project
  - Link to request it [here](https://marketplace.visualstudio.com/items?itemName=ms.vss-analytics)
  - Currently it provides information about test analytics pass & failure of the tests alongside the duration of each test. It is in the plan to provide detailed information about ETW.

## GTest Benchmark

One good option is GTest itself , GTest has performance testing ways and it can be added into the test to get the analysis about CPU/GPU and etc.
Here is a [link](https://github.com/google/benchmark) to GTest Benchmark.

## Add Pipeline step for ETW

There can be so many ways on how to implement this task in the CI/CD builds. One way could be a process as follows:

- Since we are using the tracer and the output is a text file, in the step inside the pipeline that runs the tests we need to set the output to be saved in a specific folder and set the name of the file as the buildID so that it will be unique on each run.
- Whenever there is a need to view the ETW results for a specific build we can go to that build output and view the tracer file related to that build.