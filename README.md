# CSIRO <-> NCI data managment best practice
## Description

Sandbox for CSIRO and other staff to collaborate together to find current, best-practice solutions (or just better-practice) for data managment between CSIRO and NCI systems.

Teams across CSIRO are heavy users of NCI national computing resources.  State-of-the-art modelling often generates enormous datasets, for example in the climate modelling efforts across the O&A BU.  Everyday tasks like `rsync` between CSIRO and NCI systems for processing, safe archive, and importantly keeping limited NCI space quotas clear are becoming more onerous and can be both a roadblock for continued productivity on NCI as well as a major sink of person-hours.

## Proposal
Work with an informal working group (WG) to help build best / better practice to assist.  Polish this practice before sharing more widely and engaging NCI staff.

## To-Do List

- [x] Define an initial use case
- [ ] generate better-practice approach to initial case
- [ ] implement and communicate results widely
- [ ] take on further specific use cases

## Initial use case - archive Decadal Climate Forecasting Project (DCFP) NCI Australasian Leadership Computing Grants (ALCG) raw netdcf to tape at CSIRO 
The CSIRO DCFP is currently working under a recent NCI ALCG merit allocation - https://research.csiro.au/dfp/dcfp-awarded-key-computation-by-the-nci/ .
Current storage limitations means that as the effort proceeds large collections of files will need to be archived constantly back to tape at CSIRO.

The current task is to move 8 x 11TB collections of tarfiles where each 11TB collection is a directory with:
* 96 x 112GB tar files
* 1  x 40GB tar file
* two small directories with log files

## Suggestion
WG contributors add "issues" to https://github.com/Thomas-Moore-Creative/CSIRO-NCI-data-best-practice/issues with other specific use cases
