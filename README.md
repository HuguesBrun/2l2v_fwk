# H->ZZ->2l2v code for limits

## Installation
```
#Instructions according to: https://twiki.cern.ch/twiki/bin/viewauth/CMS/SWGuideHiggsAnalysisCombinedLimit#ROOT6_SLC6_release_CMSSW_7_4_X

#CMSSW installation
export SCRAM_ARCH=slc6_amd64_gcc491
cmsrel CMSSW_7_4_7
cd CMSSW_7_4_7/src 
cmsenv

#Combine installation
git clone https://github.com/cms-analysis/HiggsAnalysis-CombinedLimit.git HiggsAnalysis/CombinedLimit
cd HiggsAnalysis/CombinedLimit
git fetch origin
git checkout v6.3.1
scramv1 b clean; scramv1 b # always make a clean build, as scram doesn't always see updates to src/LinkDef.h
cd $CMSSW_BASE/src

#2l2nu computeLimit code
mkdir -p UserCode
cd $CMSSW_BASE/src/UserCode
git clone git@github.com:<yourgithubid>/2l2v_fwk.git llvv_fwk
cd llvv_fwk
git remote add computeLimit_upstream git@github.com:HuguesBrun/2l2v_fwk.git #will be changed in the future when it will be merged to the central repo
git remote update
git merge computeLimit_upstream/HzzIn747
cd $CMSSW_BASE/src
scram b -j8
```

## How to compute limits
WARNING: be sure you have up to date computeLimit code (the newest version could be in the central repo!)
 - ask for an updated plotter for limits to the group (ran with the central code, on `submit.sh 3.30` and `3.31`)
 - copy this plotter under the name `plotter.root` in `$CMSSW_BASE/src/UserCode/llvv_fwk/test/hzz2l2v/`
 - produce the InstrMET systematics files:
```
cd $CMSSW_BASE/src/UserCode/llvv_fwk/test/hzz2l2v/macro_Intr_MET_Syst
sh produceInstrMET_syst_rootFiles.sh
```
 - run the limits (see "How to tweak the limit code" below for more information):
 ```
cd $CMSSW_BASE/src/UserCode/llvv_fwk/test/hzz2l2v/computeLimit
python optimize_WideWidth.py -p 4 #add explanation here
python optimize_WideWidth.py -p 5 #add explanation here
```

## How to tweak the limit code
 1. You may want to change the json, root files, mc or datadriven mode of the optimize_WideWidth.py code. For this, check [here](https://github.com/cms2l2v/2l2v_fwk/blob/master/test/hzz2l2v/computeLimit/optimize_WideWidth.py#L31-L40) 
 2. You may also select the signal process to use GGF or VBF (when working smoothly this won't be needed anymore) in [optimize_WideWidth.py](https://github.com/cms2l2v/2l2v_fwk/blob/master/test/hzz2l2v/computeLimit/optimize_WideWidth.py#L61-L71), then don't forget to also change [HiggsWidth.py](https://github.com/cms2l2v/2l2v_fwk/blob/master/python/HiggsWidth.py#L52-L60)
 3. You may want to change the binning of this histograms in [computeLimit.cc](https://github.com/cms2l2v/2l2v_fwk/blob/master/bin/common/computeLimit.cc#L2799-L2809). Please then check that the Inst.MET systematic has the same binning of the Mt in [the macro for instrMET syst files](https://github.com/cms2l2v/2l2v_fwk/blob/master/test/hzz2l2v/macro_Intr_MET_Syst/MakeSyst_custom_forMTandMET.C#L137-L141) and reproduce the files accordingly if needed.
 
