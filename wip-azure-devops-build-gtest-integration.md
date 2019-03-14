# Integration of GTest into the Azure DevOps Build Pipeline

> Note: This document is a work in progress and is captured for the benefit of knowledge transfer.

We were in the process of integrating gTests into the pipeline, but ran into an issue where the tests would only work if run from the xplatform\webrtc directory, and still, only if the out directory resides there as well (i.e. running .\out\target_name_folder\modules_unittests.exe).
We came to the conclusion that our options are to either:
Tweak python scripts to also download that for modules_unittests target 
 run with the defaulttarget flag or
 modify python to be able to download them via a single command and just run that before the tests. We are not completely sure what the solution is for this task, but hoping you'd have a better idea.