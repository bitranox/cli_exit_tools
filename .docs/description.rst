small toolset to properly exit a cli application:

- print the traceback information (can be set with commandline option)
- get a proper exit code from the Exception
- flush the streams, to make sure output is written in proper order
- demo how to integrate into Your cli module (see usage)
