---
title: cmake script
description: cmake script
date: 2023-11-06
weight: 30000


---
<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{%pageinfo%}}

{{%/pageinfo%}}



{{<note>}}
<!---->


```bash

```



{{</note>}}



















### 编译：debug
```bash
cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/mysql -DMYSQL_DATADIR=/usr/local/mysql/data -DDOWNLOAD_BOOST=1 -DWITH_BOOST=/usr/local/mysql-8.0.12/ -DSYSCONFDIR=/etc -DWITH_INNOBASE_STORAGE_ENGINE=1 -DWITH_PARTITION_STORAGE_ENGINE=1 -DWITH_FEDERATED_STORAGE_ENGINE=1 -DWITH_BLACKHOLE_STORAGE_ENGINE=1 -DWITH_MYISAM_STORAGE_ENGINE=1 -DWITH_EMBEDDED_SERVER=1 -DENABLE_DTRACE=0 -DENABLED_LOCAL_INFILE=1 -DDEFAULT_CHARSET=utf8mb4
```

#### 编译：debug



```bash
mysqld --initialize-insecure --user=mysql
```




## share/cmake-version/Modules

```sql
[glc@48 ~]$ tree  /db/storage/glc/local/cmake-3.28.1-linux-x86_64/share/cmake-3.28/Modules
/db/storage/glc/local/cmake-3.28.1-linux-x86_64/share/cmake-3.28/Modules
├── AddFileDependencies.cmake
├── AndroidTestUtilities
│   └── PushToAndroidDevice.cmake
├── AndroidTestUtilities.cmake
├── BasicConfigVersion-AnyNewerVersion.cmake.in
├── BasicConfigVersion-ExactVersion.cmake.in
├── BasicConfigVersion-SameMajorVersion.cmake.in
├── BasicConfigVersion-SameMinorVersion.cmake.in
├── BundleUtilities.cmake
├── CheckCCompilerFlag.cmake
├── CheckCompilerFlag.cmake
├── CheckCSourceCompiles.cmake
├── CheckCSourceRuns.cmake
├── CheckCXXCompilerFlag.cmake
├── CheckCXXSourceCompiles.cmake
├── CheckCXXSourceRuns.cmake
├── CheckCXXSymbolExists.cmake
├── CheckForPthreads.c
├── CheckFortranCompilerFlag.cmake
├── CheckFortranFunctionExists.cmake
├── CheckFortranSourceCompiles.cmake
├── CheckFortranSourceRuns.cmake
├── CheckFunctionExists.c
├── CheckFunctionExists.cmake
├── CheckIncludeFile.c.in
├── CheckIncludeFile.cmake
├── CheckIncludeFileCXX.cmake
├── CheckIncludeFile.cxx.in
├── CheckIncludeFiles.cmake
├── CheckIPOSupported
│   ├── CMakeLists-C.txt.in
│   ├── CMakeLists-CXX.txt.in
│   ├── CMakeLists-Fortran.txt.in
│   ├── foo.c
│   ├── foo.cpp
│   ├── foo.f
│   ├── main.c
│   ├── main.cpp
│   └── main.f
├── CheckIPOSupported.cmake
├── CheckLanguage.cmake
├── CheckLibraryExists.cmake
├── CheckLibraryExists.lists.in
├── CheckLinkerFlag.cmake
├── CheckOBJCCompilerFlag.cmake
├── CheckOBJCSourceCompiles.cmake
├── CheckOBJCSourceRuns.cmake
├── CheckOBJCXXCompilerFlag.cmake
├── CheckOBJCXXSourceCompiles.cmake
├── CheckOBJCXXSourceRuns.cmake
├── CheckPIESupported.cmake
├── CheckPrototypeDefinition.c.in
├── CheckPrototypeDefinition.cmake
├── CheckSizeOf.cmake
├── CheckSourceCompiles.cmake
├── CheckSourceRuns.cmake
├── CheckStructHasMember.cmake
├── CheckSymbolExists.cmake
├── CheckTypeSize.c.in
├── CheckTypeSize.cmake
├── CheckTypeSizeMap.cmake.in
├── CheckVariableExists.c
├── CheckVariableExists.cmake
├── CMakeAddFortranSubdirectory
│   ├── build_mingw.cmake.in
│   └── config_mingw.cmake.in
├── CMakeAddFortranSubdirectory.cmake
├── CMakeAddNewLanguage.txt
├── CMakeASM-ATTInformation.cmake
├── CMakeASMCompiler.cmake.in
├── CMakeASMInformation.cmake
├── CMakeASM_MARMASMInformation.cmake
├── CMakeASM_MASMInformation.cmake
├── CMakeASM_NASMInformation.cmake
├── CMakeBackwardCompatibilityC.cmake
├── CMakeBackwardCompatibilityCXX.cmake
├── CMakeBorlandFindMake.cmake
├── CMakeBuildSettings.cmake.in
├── CMakeCCompilerABI.c
├── CMakeCCompiler.cmake.in
├── CMakeCCompilerId.c.in
├── CMakeCheckCompilerFlagCommonPatterns.cmake
├── CMakeCInformation.cmake
├── CMake.cmake
├── CMakeCommonLanguageInclude.cmake
├── CMakeCompilerABI.h
├── CMakeCompilerCUDAArch.h
├── CMakeCompilerIdDetection.cmake
├── CMakeConfigurableFile.in
├── CMakeCSharpCompiler.cmake.in
├── CMakeCSharpCompilerId.cs.in
├── CMakeCSharpInformation.cmake
├── CMakeCUDACompilerABI.cu
├── CMakeCUDACompiler.cmake.in
├── CMakeCUDACompilerId.cu.in
├── CMakeCUDAInformation.cmake
├── CMakeCXXCompilerABI.cpp
├── CMakeCXXCompiler.cmake.in
├── CMakeCXXCompilerId.cpp.in
├── CMakeCXXInformation.cmake
├── CMakeDependentOption.cmake
├── CMakeDetermineASM-ATTCompiler.cmake
├── CMakeDetermineASMCompiler.cmake
├── CMakeDetermineASM_MARMASMCompiler.cmake
├── CMakeDetermineASM_MASMCompiler.cmake
├── CMakeDetermineASM_NASMCompiler.cmake
├── CMakeDetermineCCompiler.cmake
├── CMakeDetermineCompileFeatures.cmake
├── CMakeDetermineCompilerABI.cmake
├── CMakeDetermineCompiler.cmake
├── CMakeDetermineCompilerId.cmake
├── CMakeDetermineCSharpCompiler.cmake
├── CMakeDetermineCUDACompiler.cmake
├── CMakeDetermineCXXCompiler.cmake
├── CMakeDetermineFortranCompiler.cmake
├── CMakeDetermineHIPCompiler.cmake
├── CMakeDetermineISPCCompiler.cmake
├── CMakeDetermineJavaCompiler.cmake
├── CMakeDetermineOBJCCompiler.cmake
├── CMakeDetermineOBJCXXCompiler.cmake
├── CMakeDetermineRCCompiler.cmake
├── CMakeDetermineSwiftCompiler.cmake
├── CMakeDetermineSystem.cmake
├── CMakeDetermineVSServicePack.cmake
├── CMakeExpandImportedTargets.cmake
├── CMakeExportBuildSettings.cmake
├── CMakeExtraGeneratorDetermineCompilerMacrosAndIncludeDirs.cmake
├── CMakeFindBinUtils.cmake
├── CMakeFindCodeBlocks.cmake
├── CMakeFindDependencyMacro.cmake
├── CMakeFindEclipseCDT4.cmake
├── CMakeFindFrameworks.cmake
├── CMakeFindJavaCommon.cmake
├── CMakeFindKate.cmake
├── CMakeFindPackageMode.cmake
├── CMakeFindSublimeText2.cmake
├── CMakeFindWMake.cmake
├── CMakeFindXCode.cmake
├── CMakeForceCompiler.cmake
├── CMakeFortranCompilerABI.F
├── CMakeFortranCompilerABI.F90
├── CMakeFortranCompiler.cmake.in
├── CMakeFortranCompilerId.F.in
├── CMakeFortranInformation.cmake
├── CMakeGenericSystem.cmake
├── CMakeGraphVizOptions.cmake
├── CMakeHIPCompilerABI.hip
├── CMakeHIPCompiler.cmake.in
├── CMakeHIPCompilerId.hip.in
├── CMakeHIPInformation.cmake
├── CMakeImportBuildSettings.cmake
├── CMakeInitializeConfigs.cmake
├── CMakeIOSInstallCombined.cmake
├── CMakeISPCCompilerABI.ispc
├── CMakeISPCCompiler.cmake.in
├── CMakeISPCCompilerId.ispc.in
├── CMakeISPCInformation.cmake
├── CMakeJavaCompiler.cmake.in
├── CMakeJavaInformation.cmake
├── CMakeJOMFindMake.cmake
├── CMakeLanguageInformation.cmake
├── CMakeMinGWFindMake.cmake
├── CMakeMSYSFindMake.cmake
├── CMakeNinjaFindMake.cmake
├── CMakeNMakeFindMake.cmake
├── CMakeOBJCCompilerABI.m
├── CMakeOBJCCompiler.cmake.in
├── CMakeOBJCCompilerId.m.in
├── CMakeOBJCInformation.cmake
├── CMakeOBJCXXCompilerABI.mm
├── CMakeOBJCXXCompiler.cmake.in
├── CMakeOBJCXXCompilerId.mm.in
├── CMakeOBJCXXInformation.cmake
├── CMakePackageConfigHelpers.cmake
├── CMakeParseArguments.cmake
├── CMakeParseImplicitIncludeInfo.cmake
├── CMakeParseImplicitLinkInfo.cmake
├── CMakeParseLibraryArchitecture.cmake
├── CMakePlatformId.h.in
├── CMakePrintHelpers.cmake
├── CMakePrintSystemInformation.cmake
├── CMakePushCheckState.cmake
├── CMakeRCCompiler.cmake.in
├── CMakeRCInformation.cmake
├── CMakeSwiftCompiler.cmake.in
├── CMakeSwiftInformation.cmake
├── CMakeSystem.cmake.in
├── CMakeSystemSpecificInformation.cmake
├── CMakeSystemSpecificInitialize.cmake
├── CMakeTestASM-ATTCompiler.cmake
├── CMakeTestASMCompiler.cmake
├── CMakeTestASM_MARMASMCompiler.cmake
├── CMakeTestASM_MASMCompiler.cmake
├── CMakeTestASM_NASMCompiler.cmake
├── CMakeTestCCompiler.cmake
├── CMakeTestCompilerCommon.cmake
├── CMakeTestCSharpCompiler.cmake
├── CMakeTestCUDACompiler.cmake
├── CMakeTestCXXCompiler.cmake
├── CMakeTestFortranCompiler.cmake
├── CMakeTestGNU.c
├── CMakeTestHIPCompiler.cmake
├── CMakeTestISPCCompiler.cmake
├── CMakeTestJavaCompiler.cmake
├── CMakeTestOBJCCompiler.cmake
├── CMakeTestOBJCXXCompiler.cmake
├── CMakeTestRCCompiler.cmake
├── CMakeTestSwiftCompiler.cmake
├── CMakeUnixFindMake.cmake
├── CMakeVerifyManifest.cmake
├── Compiler
│   ├── Absoft-Fortran.cmake
│   ├── ADSP-C.cmake
│   ├── ADSP.cmake
│   ├── ADSP-CXX.cmake
│   ├── ADSP-DetermineCompiler.cmake
│   ├── AppleClang-ASM.cmake
│   ├── AppleClang-C.cmake
│   ├── AppleClang-C-FeatureTests.cmake
│   ├── AppleClang-CXX.cmake
│   ├── AppleClang-CXX-FeatureTests.cmake
│   ├── AppleClang-DetermineCompiler.cmake
│   ├── AppleClang-OBJC.cmake
│   ├── AppleClang-OBJCXX.cmake
│   ├── ARMCC-ASM.cmake
│   ├── ARMCC-C.cmake
│   ├── ARMCC.cmake
│   ├── ARMCC-CXX.cmake
│   ├── ARMCC-DetermineCompiler.cmake
│   ├── ARMClang-ASM.cmake
│   ├── ARMClang-C.cmake
│   ├── ARMClang-C-FeatureTests.cmake
│   ├── ARMClang.cmake
│   ├── ARMClang-CXX.cmake
│   ├── ARMClang-CXX-FeatureTests.cmake
│   ├── ARMClang-DetermineCompiler.cmake
│   ├── Borland-DetermineCompiler.cmake
│   ├── Bruce-C.cmake
│   ├── Bruce-C-DetermineCompiler.cmake
│   ├── CCur-Fortran.cmake
│   ├── Clang-ASM.cmake
│   ├── Clang-C.cmake
│   ├── Clang-C-FeatureTests.cmake
│   ├── Clang.cmake
│   ├── Clang-CUDA.cmake
│   ├── Clang-CXX.cmake
│   ├── Clang-CXX-FeatureTests.cmake
│   ├── Clang-CXX-TestableFeatures.cmake
│   ├── Clang-DetermineCompiler.cmake
│   ├── Clang-DetermineCompilerInternal.cmake
│   ├── Clang-FindBinUtils.cmake
│   ├── Clang-HIP.cmake
│   ├── Clang-OBJC.cmake
│   ├── Clang-OBJCXX.cmake
│   ├── CMakeCommonCompilerMacros.cmake
│   ├── Comeau-CXX-DetermineCompiler.cmake
│   ├── Compaq-C-DetermineCompiler.cmake
│   ├── Compaq-CXX-DetermineCompiler.cmake
│   ├── Cray-C.cmake
│   ├── CrayClang-C.cmake
│   ├── CrayClang.cmake
│   ├── CrayClang-CXX.cmake
│   ├── CrayClang-DetermineCompiler.cmake
│   ├── Cray.cmake
│   ├── Cray-CXX.cmake
│   ├── Cray-DetermineCompiler.cmake
│   ├── Cray-Fortran.cmake
│   ├── CrayPrgEnv-C.cmake
│   ├── CrayPrgEnv.cmake
│   ├── CrayPrgEnv-CXX.cmake
│   ├── CrayPrgEnv-Fortran.cmake
│   ├── Embarcadero-DetermineCompiler.cmake
│   ├── Flang-FindBinUtils.cmake
│   ├── Flang-Fortran.cmake
│   ├── Fujitsu-C.cmake
│   ├── FujitsuClang-C.cmake
│   ├── FujitsuClang.cmake
│   ├── FujitsuClang-CXX.cmake
│   ├── FujitsuClang-DetermineCompiler.cmake
│   ├── FujitsuClang-FindBinUtils.cmake
│   ├── Fujitsu.cmake
│   ├── Fujitsu-CXX.cmake
│   ├── Fujitsu-DetermineCompiler.cmake
│   ├── Fujitsu-Fortran.cmake
│   ├── G95-Fortran.cmake
│   ├── GHS-C.cmake
│   ├── GHS.cmake
│   ├── GHS-CXX.cmake
│   ├── GHS-DetermineCompiler.cmake
│   ├── GNU-ASM.cmake
│   ├── GNU-C.cmake
│   ├── GNU-C-DetermineCompiler.cmake
│   ├── GNU-C-FeatureTests.cmake
│   ├── GNU.cmake
│   ├── GNU-CXX.cmake
│   ├── GNU-CXX-DetermineCompiler.cmake
│   ├── GNU-CXX-FeatureTests.cmake
│   ├── GNU-FindBinUtils.cmake
│   ├── GNU-Fortran.cmake
│   ├── GNU-OBJC.cmake
│   ├── GNU-OBJCXX.cmake
│   ├── HP-ASM.cmake
│   ├── HP-C.cmake
│   ├── HP-C-DetermineCompiler.cmake
│   ├── HP-CXX.cmake
│   ├── HP-CXX-DetermineCompiler.cmake
│   ├── HP-Fortran.cmake
│   ├── IAR-ASM.cmake
│   ├── IAR-C.cmake
│   ├── IAR.cmake
│   ├── IAR-CXX.cmake
│   ├── IAR-DetermineCompiler.cmake
│   ├── IBMClang-ASM.cmake
│   ├── IBMClang-C.cmake
│   ├── IBMClang-C-DetermineCompiler.cmake
│   ├── IBMClang.cmake
│   ├── IBMClang-CXX.cmake
│   ├── IBMClang-CXX-DetermineCompiler.cmake
│   ├── IBMCPP-C-DetermineVersionInternal.cmake
│   ├── IBMCPP-CXX-DetermineVersionInternal.cmake
│   ├── Intel-ASM.cmake
│   ├── Intel-C.cmake
│   ├── Intel-C-FeatureTests.cmake
│   ├── Intel.cmake
│   ├── Intel-CXX.cmake
│   ├── Intel-CXX-FeatureTests.cmake
│   ├── Intel-DetermineCompiler.cmake
│   ├── Intel-Fortran.cmake
│   ├── Intel-ISPC.cmake
│   ├── IntelLLVM-ASM.cmake
│   ├── IntelLLVM-C.cmake
│   ├── IntelLLVM.cmake
│   ├── IntelLLVM-CXX.cmake
│   ├── IntelLLVM-DetermineCompiler.cmake
│   ├── IntelLLVM-FindBinUtils.cmake
│   ├── IntelLLVM-Fortran.cmake
│   ├── LCC-C.cmake
│   ├── LCC-C-DetermineCompiler.cmake
│   ├── LCC-C-FeatureTests.cmake
│   ├── LCC.cmake
│   ├── LCC-CXX.cmake
│   ├── LCC-CXX-DetermineCompiler.cmake
│   ├── LCC-CXX-FeatureTests.cmake
│   ├── LCC-FindBinUtils.cmake
│   ├── LCC-Fortran.cmake
│   ├── LLVMFlang-Fortran.cmake
│   ├── MSVC-ASM.cmake
│   ├── MSVC-C.cmake
│   ├── MSVC-C-FeatureTests.cmake
│   ├── MSVC.cmake
│   ├── MSVC-CXX.cmake
│   ├── MSVC-CXX-FeatureTests.cmake
│   ├── MSVC-DetermineCompiler.cmake
│   ├── NAG-Fortran.cmake
│   ├── NVHPC-C.cmake
│   ├── NVHPC.cmake
│   ├── NVHPC-CXX.cmake
│   ├── NVHPC-DetermineCompiler.cmake
│   ├── NVHPC-Fortran.cmake
│   ├── NVIDIA.cmake
│   ├── NVIDIA-CUDA.cmake
│   ├── NVIDIA-DetermineCompiler.cmake
│   ├── NVIDIA-HIP.cmake
│   ├── OpenWatcom-C.cmake
│   ├── OpenWatcom.cmake
│   ├── OpenWatcom-CXX.cmake
│   ├── OpenWatcom-DetermineCompiler.cmake
│   ├── OrangeC-ASM.cmake
│   ├── OrangeC-C.cmake
│   ├── OrangeC.cmake
│   ├── OrangeC-CXX.cmake
│   ├── OrangeC-DetermineCompiler.cmake
│   ├── PathScale-C.cmake
│   ├── PathScale.cmake
│   ├── PathScale-CXX.cmake
│   ├── PathScale-DetermineCompiler.cmake
│   ├── PathScale-Fortran.cmake
│   ├── PGI-C.cmake
│   ├── PGI.cmake
│   ├── PGI-CXX.cmake
│   ├── PGI-DetermineCompiler.cmake
│   ├── PGI-Fortran.cmake
│   ├── QCC-ASM.cmake
│   ├── QCC-C.cmake
│   ├── QCC-C-FeatureTests.cmake
│   ├── QCC.cmake
│   ├── QCC-CXX.cmake
│   ├── QCC-CXX-FeatureTests.cmake
│   ├── SCO-C.cmake
│   ├── SCO.cmake
│   ├── SCO-CXX.cmake
│   ├── SCO-DetermineCompiler.cmake
│   ├── SDCC-C-DetermineCompiler.cmake
│   ├── SunPro-ASM.cmake
│   ├── SunPro-C.cmake
│   ├── SunPro-C-DetermineCompiler.cmake
│   ├── SunPro-C-FeatureTests.cmake
│   ├── SunPro.cmake
│   ├── SunPro-CXX.cmake
│   ├── SunPro-CXX-DetermineCompiler.cmake
│   ├── SunPro-CXX-FeatureTests.cmake
│   ├── SunPro-Fortran.cmake
│   ├── Tasking-ASM.cmake
│   ├── Tasking-C.cmake
│   ├── Tasking.cmake
│   ├── Tasking-CXX.cmake
│   ├── Tasking-DetermineCompiler.cmake
│   ├── Tasking-FindBinUtils.cmake
│   ├── TI-ASM.cmake
│   ├── TI-C.cmake
│   ├── TI.cmake
│   ├── TI-CXX.cmake
│   ├── TI-DetermineCompiler.cmake
│   ├── TinyCC-C.cmake
│   ├── TinyCC-C-DetermineCompiler.cmake
│   ├── VisualAge-C.cmake
│   ├── VisualAge-C-DetermineCompiler.cmake
│   ├── VisualAge-CXX.cmake
│   ├── VisualAge-CXX-DetermineCompiler.cmake
│   ├── VisualAge-Fortran.cmake
│   ├── Watcom-DetermineCompiler.cmake
│   ├── XL-ASM.cmake
│   ├── XL-C.cmake
│   ├── XL-C-DetermineCompiler.cmake
│   ├── XLClang-C.cmake
│   ├── XLClang-C-DetermineCompiler.cmake
│   ├── XLClang.cmake
│   ├── XLClang-CXX.cmake
│   ├── XLClang-CXX-DetermineCompiler.cmake
│   ├── XL.cmake
│   ├── XL-CXX.cmake
│   ├── XL-CXX-DetermineCompiler.cmake
│   ├── XL-Fortran
│   │   └── cpp
│   ├── XL-Fortran.cmake
│   ├── zOS-C-DetermineCompiler.cmake
│   └── zOS-CXX-DetermineCompiler.cmake
├── CompilerId
│   ├── GHS_default.gpj.in
│   ├── GHS_lib.gpj.in
│   ├── main.swift.in
│   ├── VS-10.csproj.in
│   ├── VS-10.vcxproj.in
│   ├── VS-7.vcproj.in
│   ├── VS-Intel.vfproj.in
│   ├── VS-NsightTegra.vcxproj.in
│   └── Xcode-3.pbxproj.in
├── CPack.cmake
├── CPackComponent.cmake
├── CPackIFW.cmake
├── CPackIFWConfigureFile.cmake
├── CSharpUtilities.cmake
├── CTest.cmake
├── CTestCoverageCollectGCOV.cmake
├── CTestScriptMode.cmake
├── CTestTargets.cmake
├── CTestUseLaunchers.cmake
├── Dart.cmake
├── DartConfiguration.tcl.in
├── DeployQt4.cmake
├── Documentation.cmake
├── DummyCXXFile.cxx
├── ecos_clean.cmake
├── exportheader.cmake.in
├── ExternalData.cmake
├── ExternalData_config.cmake.in
├── ExternalProject
│   ├── cfgcmd.txt.in
│   ├── download.cmake.in
│   ├── extractfile.cmake.in
│   ├── gitclone.cmake.in
│   ├── gitupdate.cmake.in
│   ├── hgclone.cmake.in
│   ├── mkdirs.cmake.in
│   ├── PatchInfo.txt.in
│   ├── RepositoryInfo.txt.in
│   ├── shared_internal_commands.cmake
│   ├── UpdateInfo.txt.in
│   └── verify.cmake.in
├── ExternalProject.cmake
├── FeatureSummary.cmake
├── FetchContent
│   ├── CMakeLists.cmake.in
│   ├── package-config.cmake.in
│   └── package-config-version.cmake.in
├── FetchContent.cmake
├── FindALSA.cmake
├── FindArmadillo.cmake
├── FindASPELL.cmake
├── FindAVIFile.cmake
├── FindBacktrace.cmake
├── FindBISON.cmake
├── FindBLAS.cmake
├── FindBoost.cmake
├── FindBullet.cmake
├── FindBZip2.cmake
├── FindCABLE.cmake
├── FindCoin3D.cmake
├── FindCUDA
│   ├── make2cmake.cmake
│   ├── parse_cubin.cmake
│   ├── run_nvcc.cmake
│   └── select_compute_arch.cmake
├── FindCUDA.cmake
├── FindCUDAToolkit.cmake
├── FindCups.cmake
├── FindCURL.cmake
├── FindCurses.cmake
├── FindCVS.cmake
├── FindCxxTest.cmake
├── FindCygwin.cmake
├── FindDart.cmake
├── FindDCMTK.cmake
├── FindDevIL.cmake
├── FindDoxygen.cmake
├── FindEnvModules.cmake
├── FindEXPAT.cmake
├── FindFLEX.cmake
├── FindFLTK2.cmake
├── FindFLTK.cmake
├── FindFontconfig.cmake
├── FindFreetype.cmake
├── FindGCCXML.cmake
├── FindGDAL.cmake
├── FindGettext.cmake
├── FindGIF.cmake
├── FindGit.cmake
├── FindGLEW.cmake
├── FindGLU.cmake
├── FindGLUT.cmake
├── FindGnuplot.cmake
├── FindGnuTLS.cmake
├── FindGSL.cmake
├── FindGTest.cmake
├── FindGTK2.cmake
├── FindGTK.cmake
├── FindHDF5.cmake
├── FindHg.cmake
├── FindHSPELL.cmake
├── FindHTMLHelp.cmake
├── FindIce.cmake
├── FindIconv.cmake
├── FindIcotool.cmake
├── FindICU.cmake
├── FindImageMagick.cmake
├── FindIntl.cmake
├── FindJasper.cmake
├── FindJava.cmake
├── FindJNI.cmake
├── FindJPEG.cmake
├── FindKDE3.cmake
├── FindKDE4.cmake
├── FindLAPACK.cmake
├── FindLATEX.cmake
├── FindLibArchive.cmake
├── FindLibinput.cmake
├── FindLibLZMA.cmake
├── FindLibXml2.cmake
├── FindLibXslt.cmake
├── FindLTTngUST.cmake
├── FindLua50.cmake
├── FindLua51.cmake
├── FindLua.cmake
├── FindMatlab.cmake
├── FindMFC.cmake
├── FindMotif.cmake
├── FindMPEG2.cmake
├── FindMPEG.cmake
├── FindMPI
│   ├── fortranparam_mpi.f90.in
│   ├── libver_mpi.c
│   ├── libver_mpi.f90.in
│   ├── mpiver.f90.in
│   ├── test_mpi.c
│   └── test_mpi.f90.in
├── FindMPI.cmake
├── FindMsys.cmake
├── FindODBC.cmake
├── FindOpenACC.cmake
├── FindOpenAL.cmake
├── FindOpenCL.cmake
├── FindOpenGL.cmake
├── FindOpenMP.cmake
├── FindOpenSceneGraph.cmake
├── FindOpenSP.cmake
├── FindOpenSSL.cmake
├── FindOpenThreads.cmake
├── FindosgAnimation.cmake
├── Findosg.cmake
├── FindosgDB.cmake
├── Findosg_functions.cmake
├── FindosgFX.cmake
├── FindosgGA.cmake
├── FindosgIntrospection.cmake
├── FindosgManipulator.cmake
├── FindosgParticle.cmake
├── FindosgPresentation.cmake
├── FindosgProducer.cmake
├── FindosgQt.cmake
├── FindosgShadow.cmake
├── FindosgSim.cmake
├── FindosgTerrain.cmake
├── FindosgText.cmake
├── FindosgUtil.cmake
├── FindosgViewer.cmake
├── FindosgVolume.cmake
├── FindosgWidget.cmake
├── FindPackageHandleStandardArgs.cmake
├── FindPackageMessage.cmake
├── FindPatch.cmake
├── FindPerl.cmake
├── FindPerlLibs.cmake
├── FindPHP4.cmake
├── FindPhysFS.cmake
├── FindPike.cmake
├── FindPkgConfig.cmake
├── FindPNG.cmake
├── FindPostgreSQL.cmake
├── FindProducer.cmake
├── FindProtobuf.cmake
├── FindPython
│   └── Support.cmake
├── FindPython2.cmake
├── FindPython3.cmake
├── FindPython.cmake
├── FindPythonInterp.cmake
├── FindPythonLibs.cmake
├── FindQt3.cmake
├── FindQt4.cmake
├── FindQt.cmake
├── FindQuickTime.cmake
├── FindRTI.cmake
├── FindRuby.cmake
├── FindSDL.cmake
├── FindSDL_gfx.cmake
├── FindSDL_image.cmake
├── FindSDL_mixer.cmake
├── FindSDL_net.cmake
├── FindSDL_sound.cmake
├── FindSDL_ttf.cmake
├── FindSelfPackers.cmake
├── FindSQLite3.cmake
├── FindSquish.cmake
├── FindSubversion.cmake
├── FindSWIG.cmake
├── FindTCL.cmake
├── FindTclsh.cmake
├── FindTclStub.cmake
├── FindThreads.cmake
├── FindTIFF.cmake
├── FindUnixCommands.cmake
├── FindVulkan.cmake
├── FindWget.cmake
├── FindWish.cmake
├── FindwxWidgets.cmake
├── FindwxWindows.cmake
├── FindX11.cmake
├── FindXalanC.cmake
├── FindXCTest.cmake
├── FindXercesC.cmake
├── FindXMLRPC.cmake
├── FindZLIB.cmake
├── FLTKCompatibility.cmake
├── FortranCInterface
│   ├── call_mod.f90
│   ├── call_sub.f
│   ├── CMakeLists.txt
│   ├── Detect.cmake
│   ├── Input.cmake.in
│   ├── Macro.h.in
│   ├── main.F
│   ├── my_module_.c
│   ├── mymodule_.c
│   ├── MY_MODULE.c
│   ├── MYMODULE.c
│   ├── my_module.f90
│   ├── mymodule.f90
│   ├── my_sub.f
│   ├── mysub.f
│   ├── Output.cmake.in
│   ├── symbol.c.in
│   └── Verify
│       ├── CMakeLists.txt
│       ├── main.c
│       ├── VerifyC.c
│       ├── VerifyCXX.cxx
│       └── VerifyFortran.f
├── FortranCInterface.cmake
├── GenerateExportHeader.cmake
├── GetPrerequisites.cmake
├── GNUInstallDirs.cmake
├── GoogleTestAddTests.cmake
├── GoogleTest.cmake
├── InstallRequiredSystemLibraries.cmake
├── IntelVSImplicitPath
│   ├── CMakeLists.txt
│   ├── detect.cmake
│   └── hello.f
├── Internal
│   ├── CheckCompilerFlag.cmake
│   ├── CheckFlagCommonConfig.cmake
│   ├── CheckLinkerFlag.cmake
│   ├── CheckSourceCompiles.cmake
│   ├── CheckSourceRuns.cmake
│   ├── CMakeCUDAArchitecturesAll.cmake
│   ├── CMakeCUDAArchitecturesNative.cmake
│   ├── CMakeCUDAArchitecturesValidate.cmake
│   ├── CMakeCUDAFilterImplicitLibs.cmake
│   ├── CMakeCUDAFindToolkit.cmake
│   ├── CMakeNVCCFilterImplicitInfo.cmake
│   ├── CMakeNVCCParseImplicitInfo.cmake
│   ├── CMakeTryCompilerOrLinkerFlag.cmake
│   ├── CPack
│   │   ├── CPack.background.png.in
│   │   ├── CPackDeb.cmake
│   │   ├── CPack.Description.plist.in
│   │   ├── CPack.distribution.dist.in
│   │   ├── CPack.DS_Store.in
│   │   ├── CPackExternal.cmake
│   │   ├── CPackFreeBSD.cmake
│   │   ├── CPack.Info.plist.in
│   │   ├── CPackNuGet.cmake
│   │   ├── CPack.NuGet.nuspec.in
│   │   ├── CPack.OSXX11.Info.plist.in
│   │   ├── CPack.OSXX11.main.scpt.in
│   │   ├── CPackRPM.cmake
│   │   ├── CPack.RuntimeScript.in
│   │   ├── CPack.STGZ_Header.sh.in
│   │   ├── CPack.VolumeIcon.icns.in
│   │   ├── CPackWIX.cmake
│   │   ├── CPackZIP.cmake
│   │   ├── ISComponents.pas
│   │   ├── ISScript.template.in
│   │   ├── NSIS.InstallOptions.ini.in
│   │   ├── NSIS.template.in
│   │   └── WIX.template.in
│   ├── FeatureTesting.cmake
│   ├── HeaderpadWorkaround.cmake
│   └── OSRelease
│       ├── 010-TryOldCentOS.cmake
│       └── 020-TryDebianVersion.cmake
├── ITKCompatibility.cmake
├── kde3init_dummy.cpp.in
├── KDE3Macros.cmake
├── kde3uic.cmake
├── MacOSXBundleInfo.plist.in
├── MacOSXFrameworkInfo.plist.in
├── MacroAddFileDependencies.cmake
├── MatlabTestsRedirect.cmake
├── Platform
│   ├── ADSP-C.cmake
│   ├── ADSP.cmake
│   ├── ADSP-Common.cmake
│   ├── ADSP-CXX.cmake
│   ├── ADSP-Determine.cmake
│   ├── AIX
│   │   └── ExportImportList
│   ├── AIX-Clang-C.cmake
│   ├── AIX-Clang-CXX.cmake
│   ├── AIX.cmake
│   ├── AIX-GNU-ASM.cmake
│   ├── AIX-GNU-C.cmake
│   ├── AIX-GNU.cmake
│   ├── AIX-GNU-CXX.cmake
│   ├── AIX-GNU-Fortran.cmake
│   ├── AIX-IBMClang-C.cmake
│   ├── AIX-IBMClang.cmake
│   ├── AIX-IBMClang-CXX.cmake
│   ├── AIX-Initialize.cmake
│   ├── AIX-VisualAge-C.cmake
│   ├── AIX-VisualAge-CXX.cmake
│   ├── AIX-VisualAge-Fortran.cmake
│   ├── AIX-XL-ASM.cmake
│   ├── AIX-XL-C.cmake
│   ├── AIX-XLClang-C.cmake
│   ├── AIX-XLClang.cmake
│   ├── AIX-XLClang-CXX.cmake
│   ├── AIX-XL.cmake
│   ├── AIX-XL-CXX.cmake
│   ├── AIX-XL-Fortran.cmake
│   ├── Android
│   │   ├── abi-arm64-v8a-Clang.cmake
│   │   ├── abi-arm64-v8a-GNU.cmake
│   │   ├── abi-armeabi-Clang.cmake
│   │   ├── abi-armeabi-GNU.cmake
│   │   ├── abi-armeabi-v6-Clang.cmake
│   │   ├── abi-armeabi-v6-GNU.cmake
│   │   ├── abi-armeabi-v7a-Clang.cmake
│   │   ├── abi-armeabi-v7a-GNU.cmake
│   │   ├── abi-common-Clang.cmake
│   │   ├── abi-common.cmake
│   │   ├── abi-common-GNU.cmake
│   │   ├── abi-mips64-Clang.cmake
│   │   ├── abi-mips64-GNU.cmake
│   │   ├── abi-mips-Clang.cmake
│   │   ├── abi-mips-GNU.cmake
│   │   ├── abi-x86_64-Clang.cmake
│   │   ├── abi-x86_64-GNU.cmake
│   │   ├── abi-x86-Clang.cmake
│   │   ├── abi-x86-GNU.cmake
│   │   ├── Determine-Compiler.cmake
│   │   ├── Determine-Compiler-NDK.cmake
│   │   ├── Determine-Compiler-Standalone.cmake
│   │   ├── ndk-stl-c++.cmake
│   │   ├── ndk-stl-c++_shared.cmake
│   │   ├── ndk-stl-c++_static.cmake
│   │   ├── ndk-stl-gabi++.cmake
│   │   ├── ndk-stl-gabi++_shared.cmake
│   │   ├── ndk-stl-gabi++_static.cmake
│   │   ├── ndk-stl-gnustl.cmake
│   │   ├── ndk-stl-gnustl_shared.cmake
│   │   ├── ndk-stl-gnustl_static.cmake
│   │   ├── ndk-stl-none.cmake
│   │   ├── ndk-stl-stlport.cmake
│   │   ├── ndk-stl-stlport_shared.cmake
│   │   ├── ndk-stl-stlport_static.cmake
│   │   ├── ndk-stl-system.cmake
│   │   └── VCXProjInspect.vcxproj.in
│   ├── Android-Clang-ASM.cmake
│   ├── Android-Clang-C.cmake
│   ├── Android-Clang.cmake
│   ├── Android-Clang-CXX.cmake
│   ├── Android.cmake
│   ├── Android-Common.cmake
│   ├── Android-Determine-C.cmake
│   ├── Android-Determine.cmake
│   ├── Android-Determine-CXX.cmake
│   ├── Android-GNU-C.cmake
│   ├── Android-GNU.cmake
│   ├── Android-GNU-CXX.cmake
│   ├── Android-Initialize.cmake
│   ├── Apple-Absoft-Fortran.cmake
│   ├── Apple-AppleClang-ASM.cmake
│   ├── Apple-AppleClang-C.cmake
│   ├── Apple-AppleClang-CXX.cmake
│   ├── Apple-AppleClang-OBJC.cmake
│   ├── Apple-AppleClang-OBJCXX.cmake
│   ├── Apple-Apple-Swift.cmake
│   ├── Apple-Clang-ASM.cmake
│   ├── Apple-Clang-C.cmake
│   ├── Apple-Clang.cmake
│   ├── Apple-Clang-CXX.cmake
│   ├── Apple-Clang-OBJC.cmake
│   ├── Apple-Clang-OBJCXX.cmake
│   ├── Apple-GNU-C.cmake
│   ├── Apple-GNU.cmake
│   ├── Apple-GNU-CXX.cmake
│   ├── Apple-GNU-Fortran.cmake
│   ├── Apple-GNU-OBJC.cmake
│   ├── Apple-GNU-OBJCXX.cmake
│   ├── Apple-Intel-C.cmake
│   ├── Apple-Intel.cmake
│   ├── Apple-Intel-CXX.cmake
│   ├── Apple-Intel-Fortran.cmake
│   ├── Apple-IntelLLVM-C.cmake
│   ├── Apple-IntelLLVM.cmake
│   ├── Apple-IntelLLVM-CXX.cmake
│   ├── Apple-IntelLLVM-Fortran.cmake
│   ├── Apple-NAG-Fortran.cmake
│   ├── Apple-NVIDIA-CUDA.cmake
│   ├── Apple-PGI-C.cmake
│   ├── Apple-PGI.cmake
│   ├── Apple-PGI-CXX.cmake
│   ├── Apple-PGI-Fortran.cmake
│   ├── Apple-VisualAge-C.cmake
│   ├── Apple-VisualAge-CXX.cmake
│   ├── Apple-XL-C.cmake
│   ├── Apple-XL-CXX.cmake
│   ├── ARTOS.cmake
│   ├── ARTOS-GNU-C.cmake
│   ├── BeOS.cmake
│   ├── BeOS-Initialize.cmake
│   ├── BlueGeneL.cmake
│   ├── BlueGeneL-Initialize.cmake
│   ├── BlueGeneP-base.cmake
│   ├── BlueGeneP-dynamic.cmake
│   ├── BlueGeneP-dynamic-GNU-C.cmake
│   ├── BlueGeneP-dynamic-GNU-CXX.cmake
│   ├── BlueGeneP-dynamic-GNU-Fortran.cmake
│   ├── BlueGeneP-dynamic-Initialize.cmake
│   ├── BlueGeneP-dynamic-XL-C.cmake
│   ├── BlueGeneP-dynamic-XL-CXX.cmake
│   ├── BlueGeneP-dynamic-XL-Fortran.cmake
│   ├── BlueGeneP-static.cmake
│   ├── BlueGeneP-static-GNU-C.cmake
│   ├── BlueGeneP-static-GNU-CXX.cmake
│   ├── BlueGeneP-static-GNU-Fortran.cmake
│   ├── BlueGeneP-static-Initialize.cmake
│   ├── BlueGeneP-static-XL-C.cmake
│   ├── BlueGeneP-static-XL-CXX.cmake
│   ├── BlueGeneP-static-XL-Fortran.cmake
│   ├── BlueGeneQ-base.cmake
│   ├── BlueGeneQ-dynamic.cmake
│   ├── BlueGeneQ-dynamic-GNU-C.cmake
│   ├── BlueGeneQ-dynamic-GNU-CXX.cmake
│   ├── BlueGeneQ-dynamic-GNU-Fortran.cmake
│   ├── BlueGeneQ-dynamic-Initialize.cmake
│   ├── BlueGeneQ-dynamic-XL-C.cmake
│   ├── BlueGeneQ-dynamic-XL-CXX.cmake
│   ├── BlueGeneQ-dynamic-XL-Fortran.cmake
│   ├── BlueGeneQ-static.cmake
│   ├── BlueGeneQ-static-GNU-C.cmake
│   ├── BlueGeneQ-static-GNU-CXX.cmake
│   ├── BlueGeneQ-static-GNU-Fortran.cmake
│   ├── BlueGeneQ-static-Initialize.cmake
│   ├── BlueGeneQ-static-XL-C.cmake
│   ├── BlueGeneQ-static-XL-CXX.cmake
│   ├── BlueGeneQ-static-XL-Fortran.cmake
│   ├── BSDOS.cmake
│   ├── BSDOS-Initialize.cmake
│   ├── Catamount.cmake
│   ├── Catamount-Initialize.cmake
│   ├── CrayLinuxEnvironment.cmake
│   ├── CrayLinuxEnvironment-Initialize.cmake
│   ├── CYGWIN-Clang-C.cmake
│   ├── CYGWIN-Clang-CXX.cmake
│   ├── CYGWIN.cmake
│   ├── CYGWIN-Determine-CXX.cmake
│   ├── CYGWIN-GNU-C.cmake
│   ├── CYGWIN-GNU.cmake
│   ├── CYGWIN-GNU-CXX.cmake
│   ├── CYGWIN-GNU-Fortran.cmake
│   ├── CYGWIN-Initialize.cmake
│   ├── CYGWIN-windres.cmake
│   ├── Darwin.cmake
│   ├── Darwin-Determine-CXX.cmake
│   ├── Darwin-Initialize.cmake
│   ├── DOS.cmake
│   ├── DOS-Initialize.cmake
│   ├── DOS-OpenWatcom-C.cmake
│   ├── DOS-OpenWatcom.cmake
│   ├── DOS-OpenWatcom-CXX.cmake
│   ├── DragonFly.cmake
│   ├── DragonFly-Initialize.cmake
│   ├── eCos.cmake
│   ├── eCos-Initialize.cmake
│   ├── Euros.cmake
│   ├── FreeBSD.cmake
│   ├── FreeBSD-Determine-CXX.cmake
│   ├── FreeBSD-Initialize.cmake
│   ├── Fuchsia.cmake
│   ├── Fuchsia-Initialize.cmake
│   ├── gas.cmake
│   ├── Generic-ADSP-ASM.cmake
│   ├── Generic-ADSP-C.cmake
│   ├── Generic-ADSP-Common.cmake
│   ├── Generic-ADSP-CXX.cmake
│   ├── Generic.cmake
│   ├── Generic-ELF.cmake
│   ├── Generic-SDCC-C.cmake
│   ├── GHS-MULTI.cmake
│   ├── GHS-MULTI-Determine.cmake
│   ├── GNU.cmake
│   ├── GNU-Initialize.cmake
│   ├── GNUtoMS_lib.bat.in
│   ├── GNUtoMS_lib.cmake
│   ├── Haiku.cmake
│   ├── Haiku-Initialize.cmake
│   ├── HP-UX.cmake
│   ├── HP-UX-GNU-ASM.cmake
│   ├── HP-UX-GNU-C.cmake
│   ├── HP-UX-GNU.cmake
│   ├── HP-UX-GNU-CXX.cmake
│   ├── HP-UX-GNU-Fortran.cmake
│   ├── HP-UX-HP-ASM.cmake
│   ├── HP-UX-HP-C.cmake
│   ├── HP-UX-HP.cmake
│   ├── HP-UX-HP-CXX.cmake
│   ├── HP-UX-HP-Fortran.cmake
│   ├── HP-UX-Initialize.cmake
│   ├── iOS.cmake
│   ├── iOS-Determine-CXX.cmake
│   ├── iOS-Initialize.cmake
│   ├── kFreeBSD.cmake
│   ├── kFreeBSD-Initialize.cmake
│   ├── Linux-Absoft-Fortran.cmake
│   ├── Linux-CCur-Fortran.cmake
│   ├── Linux-Clang-C.cmake
│   ├── Linux-Clang-CXX.cmake
│   ├── Linux.cmake
│   ├── Linux-como.cmake
│   ├── Linux-Determine-CXX.cmake
│   ├── Linux-Fujitsu-C.cmake
│   ├── Linux-Fujitsu.cmake
│   ├── Linux-Fujitsu-CXX.cmake
│   ├── Linux-GNU-C.cmake
│   ├── Linux-GNU.cmake
│   ├── Linux-GNU-CXX.cmake
│   ├── Linux-GNU-Fortran.cmake
│   ├── Linux-Initialize.cmake
│   ├── Linux-Intel-C.cmake
│   ├── Linux-Intel.cmake
│   ├── Linux-Intel-CXX.cmake
│   ├── Linux-Intel-Fortran.cmake
│   ├── Linux-IntelLLVM-C.cmake
│   ├── Linux-IntelLLVM.cmake
│   ├── Linux-IntelLLVM-CXX.cmake
│   ├── Linux-IntelLLVM-Fortran.cmake
│   ├── Linux-LCC-C.cmake
│   ├── Linux-LCC.cmake
│   ├── Linux-LCC-CXX.cmake
│   ├── Linux-LCC-Fortran.cmake
│   ├── Linux-NAG-Fortran.cmake
│   ├── Linux-NVHPC-C.cmake
│   ├── Linux-NVHPC.cmake
│   ├── Linux-NVHPC-CXX.cmake
│   ├── Linux-NVHPC-Fortran.cmake
│   ├── Linux-OpenWatcom-C.cmake
│   ├── Linux-OpenWatcom.cmake
│   ├── Linux-OpenWatcom-CXX.cmake
│   ├── Linux-PathScale-C.cmake
│   ├── Linux-PathScale.cmake
│   ├── Linux-PathScale-CXX.cmake
│   ├── Linux-PathScale-Fortran.cmake
│   ├── Linux-PGI-C.cmake
│   ├── Linux-PGI.cmake
│   ├── Linux-PGI-CXX.cmake
│   ├── Linux-PGI-Fortran.cmake
│   ├── Linux-SunPro-CXX.cmake
│   ├── Linux-TinyCC-C.cmake
│   ├── Linux-VisualAge-C.cmake
│   ├── Linux-VisualAge-CXX.cmake
│   ├── Linux-VisualAge-Fortran.cmake
│   ├── Linux-XL-C.cmake
│   ├── Linux-XL-CXX.cmake
│   ├── Linux-XL-Fortran.cmake
│   ├── Midipix.cmake
│   ├── Midipix-Initialize.cmake
│   ├── MirBSD.cmake
│   ├── MP-RAS.cmake
│   ├── MP-RAS-Initialize.cmake
│   ├── MSYS-Clang-C.cmake
│   ├── MSYS-Clang-CXX.cmake
│   ├── MSYS.cmake
│   ├── MSYS-Determine-CXX.cmake
│   ├── MSYS-GNU-C.cmake
│   ├── MSYS-GNU.cmake
│   ├── MSYS-GNU-CXX.cmake
│   ├── MSYS-GNU-Fortran.cmake
│   ├── MSYS-Initialize.cmake
│   ├── MSYS-windres.cmake
│   ├── NetBSD.cmake
│   ├── NetBSD-Initialize.cmake
│   ├── OpenBSD.cmake
│   ├── OpenBSD-Initialize.cmake
│   ├── OpenVMS.cmake
│   ├── OpenVMS-Initialize.cmake
│   ├── OS2.cmake
│   ├── OS2-OpenWatcom-C.cmake
│   ├── OS2-OpenWatcom.cmake
│   ├── OS2-OpenWatcom-CXX.cmake
│   ├── OSF1.cmake
│   ├── OSF1-Initialize.cmake
│   ├── QNX.cmake
│   ├── QNX-Initialize.cmake
│   ├── RISCos.cmake
│   ├── RISCos-Initialize.cmake
│   ├── SCO_SV.cmake
│   ├── SCO_SV-Initialize.cmake
│   ├── SerenityOS-Clang-ASM.cmake
│   ├── SerenityOS-Clang-C.cmake
│   ├── SerenityOS-Clang-CXX.cmake
│   ├── SerenityOS.cmake
│   ├── SerenityOS-GNU-ASM.cmake
│   ├── SerenityOS-GNU-C.cmake
│   ├── SerenityOS-GNU.cmake
│   ├── SerenityOS-GNU-CXX.cmake
│   ├── SerenityOS-Initialize.cmake
│   ├── SINIX.cmake
│   ├── SINIX-Initialize.cmake
│   ├── SunOS-Clang-C.cmake
│   ├── SunOS-Clang-CXX.cmake
│   ├── SunOS.cmake
│   ├── SunOS-GNU-C.cmake
│   ├── SunOS-GNU.cmake
│   ├── SunOS-GNU-CXX.cmake
│   ├── SunOS-GNU-Fortran.cmake
│   ├── SunOS-Initialize.cmake
│   ├── SunOS-PathScale-C.cmake
│   ├── SunOS-PathScale.cmake
│   ├── SunOS-PathScale-CXX.cmake
│   ├── SunOS-PathScale-Fortran.cmake
│   ├── syllable.cmake
│   ├── syllable-Initialize.cmake
│   ├── Tru64.cmake
│   ├── Tru64-Initialize.cmake
│   ├── tvOS.cmake
│   ├── tvOS-Determine-CXX.cmake
│   ├── tvOS-Initialize.cmake
│   ├── ULTRIX.cmake
│   ├── ULTRIX-Initialize.cmake
│   ├── UnixPaths.cmake
│   ├── UNIX_SV.cmake
│   ├── UNIX_SV-Initialize.cmake
│   ├── UnixWare.cmake
│   ├── UnixWare-Initialize.cmake
│   ├── visionOS.cmake
│   ├── visionOS-Determine-CXX.cmake
│   ├── visionOS-Initialize.cmake
│   ├── watchOS.cmake
│   ├── watchOS-Determine-CXX.cmake
│   ├── watchOS-Initialize.cmake
│   ├── Windows3x.cmake
│   ├── Windows3x-OpenWatcom-C.cmake
│   ├── Windows3x-OpenWatcom.cmake
│   ├── Windows3x-OpenWatcom-CXX.cmake
│   ├── Windows-Apple-Swift.cmake
│   ├── Windows-Borland-C.cmake
│   ├── Windows-Borland-CXX.cmake
│   ├── WindowsCE.cmake
│   ├── WindowsCE-Initialize.cmake
│   ├── WindowsCE-MSVC-C.cmake
│   ├── WindowsCE-MSVC-CXX.cmake
│   ├── Windows-Clang-ASM.cmake
│   ├── Windows-Clang-C.cmake
│   ├── Windows-Clang.cmake
│   ├── Windows-Clang-CXX.cmake
│   ├── Windows-Clang-HIP.cmake
│   ├── Windows.cmake
│   ├── Windows-Determine-CXX.cmake
│   ├── Windows-df.cmake
│   ├── Windows-Embarcadero-C.cmake
│   ├── Windows-Embarcadero.cmake
│   ├── Windows-Embarcadero-CXX.cmake
│   ├── Windows-Flang-Fortran.cmake
│   ├── Windows-G95-Fortran.cmake
│   ├── Windows-GNU-ASM.cmake
│   ├── Windows-GNU-C-ABI.cmake
│   ├── Windows-GNU-C.cmake
│   ├── Windows-GNU.cmake
│   ├── Windows-GNU-CXX-ABI.cmake
│   ├── Windows-GNU-CXX.cmake
│   ├── Windows-GNU-Fortran-ABI.cmake
│   ├── Windows-GNU-Fortran.cmake
│   ├── Windows-Initialize.cmake
│   ├── Windows-Intel-ASM.cmake
│   ├── Windows-Intel-C.cmake
│   ├── Windows-Intel.cmake
│   ├── Windows-Intel-CXX.cmake
│   ├── Windows-Intel-Fortran.cmake
│   ├── Windows-Intel-ISPC.cmake
│   ├── Windows-IntelLLVM-ASM.cmake
│   ├── Windows-IntelLLVM-C.cmake
│   ├── Windows-IntelLLVM.cmake
│   ├── Windows-IntelLLVM-CXX.cmake
│   ├── Windows-IntelLLVM-Fortran.cmake
│   ├── WindowsKernelModeDriver.cmake
│   ├── WindowsKernelModeDriver-Initialize.cmake
│   ├── WindowsKernelModeDriver-MSVC-C.cmake
│   ├── WindowsKernelModeDriver-MSVC-CXX.cmake
│   ├── Windows-LLVMFlang-Fortran.cmake
│   ├── Windows-MSVC-C.cmake
│   ├── Windows-MSVC.cmake
│   ├── Windows-MSVC-CXX.cmake
│   ├── Windows-NVIDIA-CUDA.cmake
│   ├── Windows-OpenWatcom-C.cmake
│   ├── Windows-OpenWatcom.cmake
│   ├── Windows-OpenWatcom-CXX.cmake
│   ├── Windows-OrangeC-C.cmake
│   ├── Windows-OrangeC.cmake
│   ├── Windows-OrangeC-CXX.cmake
│   ├── WindowsPaths.cmake
│   ├── Windows-PGI-C.cmake
│   ├── Windows-PGI.cmake
│   ├── Windows-PGI-Fortran.cmake
│   ├── WindowsPhone-Clang-ASM.cmake
│   ├── WindowsPhone-Clang-C.cmake
│   ├── WindowsPhone-Clang-CXX.cmake
│   ├── WindowsPhone.cmake
│   ├── WindowsPhone-GNU-ASM.cmake
│   ├── WindowsPhone-GNU-C.cmake
│   ├── WindowsPhone-GNU-CXX.cmake
│   ├── WindowsPhone-Initialize.cmake
│   ├── WindowsPhone-MSVC-C.cmake
│   ├── WindowsPhone-MSVC-CXX.cmake
│   ├── WindowsStore-Clang-ASM.cmake
│   ├── WindowsStore-Clang-C.cmake
│   ├── WindowsStore-Clang-CXX.cmake
│   ├── WindowsStore.cmake
│   ├── WindowsStore-GNU-ASM.cmake
│   ├── WindowsStore-GNU-C.cmake
│   ├── WindowsStore-GNU-CXX.cmake
│   ├── WindowsStore-Initialize.cmake
│   ├── WindowsStore-MSVC-C.cmake
│   ├── WindowsStore-MSVC-CXX.cmake
│   ├── Windows-Watcom-C.cmake
│   ├── Windows-Watcom-CXX.cmake
│   ├── Windows-windres.cmake
│   ├── Xenix.cmake
│   └── Xenix-Initialize.cmake
├── ProcessorCount.cmake
├── Qt4ConfigDependentSettings.cmake
├── Qt4Macros.cmake
├── readme.txt
├── SelectLibraryConfigurations.cmake
├── Squish4RunTestCase.bat
├── Squish4RunTestCase.sh
├── SquishRunTestCase.bat
├── SquishRunTestCase.sh
├── SquishTestScript.cmake
├── SystemInformation.cmake
├── SystemInformation.in
├── TestBigEndian.cmake
├── TestCXXAcceptsFlag.cmake
├── TestEndianess.c.in
├── TestForANSIForScope.cmake
├── TestForAnsiForScope.cxx
├── TestForANSIStreamHeaders.cmake
├── TestForANSIStreamHeaders.cxx
├── TestForSSTREAM.cmake
├── TestForSSTREAM.cxx
├── TestForSTDNamespace.cmake
├── TestForSTDNamespace.cxx
├── UseEcos.cmake
├── UseJava
│   ├── ClassFilelist.cmake
│   ├── ClearClassFiles.cmake
│   ├── javaTargets.cmake.in
│   └── Symlinks.cmake
├── UseJava.cmake
├── UsePkgConfig.cmake
├── UseQt4.cmake
├── UseSWIG
│   └── ManageSupportFiles.cmake
├── UseSWIG.cmake
├── UsewxWidgets.cmake
├── Use_wxWindows.cmake
├── VTKCompatibility.cmake
├── WriteBasicConfigVersionFile.cmake
└── WriteCompilerDetectionHeader.cmake

22 directories, 1208 files



```



### FindGit.cmake
```cmake
# Distributed under the OSI-approved BSD 3-Clause License.  See accompanying
# file Copyright.txt or https://cmake.org/licensing for details.

#[=======================================================================[.rst:
FindGit
-------

The module defines the following variables:

``GIT_EXECUTABLE``
  Path to Git command-line client.
``Git_FOUND``, ``GIT_FOUND``
  True if the Git command-line client was found.
``GIT_VERSION_STRING``
  The version of Git found.

.. versionadded:: 3.14
  The module defines the following ``IMPORTED`` targets (when
  :prop_gbl:`CMAKE_ROLE` is ``PROJECT``):

``Git::Git``
  Executable of the Git command-line client.

Example usage:

.. code-block:: cmake

   find_package(Git)
   if(Git_FOUND)
     message("Git found: ${GIT_EXECUTABLE}")
   endif()
#]=======================================================================]

# Look for 'git'
#
set(git_names git)

# Prefer .cmd variants on Windows unless running in a Makefile
# in the MSYS shell.
#
if(CMAKE_HOST_WIN32)
  if(NOT CMAKE_GENERATOR MATCHES "MSYS")
    set(git_names git.cmd git)
    # GitHub search path for Windows
    file(GLOB github_path
      "$ENV{LOCALAPPDATA}/Github/PortableGit*/cmd"
      "$ENV{LOCALAPPDATA}/Github/PortableGit*/bin"
      )
    # SourceTree search path for Windows
    set(_git_sourcetree_path "$ENV{LOCALAPPDATA}/Atlassian/SourceTree/git_local/bin")
  endif()
endif()

# First search the PATH and specific locations.
find_program(GIT_EXECUTABLE
  NAMES ${git_names}
  PATHS ${github_path} ${_git_sourcetree_path}
  DOC "Git command line client"
  )

if(CMAKE_HOST_WIN32)
  # Now look for installations in Git/ directories under typical installation
  # prefixes on Windows.  Exclude PATH from this search because VS 2017's
  # command prompt happens to have a PATH entry with a Git/ subdirectory
  # containing a minimal git not meant for general use.
  find_program(GIT_EXECUTABLE
    NAMES ${git_names}
    PATH_SUFFIXES Git/cmd Git/bin
    NO_SYSTEM_ENVIRONMENT_PATH
    DOC "Git command line client"
    )
endif()

mark_as_advanced(GIT_EXECUTABLE)

unset(git_names)
unset(_git_sourcetree_path)

if(GIT_EXECUTABLE)
  # Avoid querying the version if we've already done that this run. For
  # projects that use things like ExternalProject or FetchContent heavily,
  # this saving can be measurable on some platforms.
  #
  # This is an internal property, projects must not try to use it.
  # We don't want this stored in the cache because it might still change
  # between CMake runs, but it shouldn't change during a run for a given
  # git executable location.
  set(__doGitVersionCheck TRUE)
  get_property(__gitVersionProp GLOBAL
    PROPERTY _CMAKE_FindGit_GIT_EXECUTABLE_VERSION
  )
  if(__gitVersionProp)
    list(GET __gitVersionProp 0 __gitExe)
    list(GET __gitVersionProp 1 __gitVersion)
    if(__gitExe STREQUAL GIT_EXECUTABLE AND NOT __gitVersion STREQUAL "")
      set(GIT_VERSION_STRING "${__gitVersion}")
      set(__doGitVersionCheck FALSE)
    endif()
    unset(__gitExe)
    unset(__gitVersion)
  endif()
  unset(__gitVersionProp)

  if(__doGitVersionCheck)
    execute_process(COMMAND ${GIT_EXECUTABLE} --version
                    OUTPUT_VARIABLE git_version
                    ERROR_QUIET
                    OUTPUT_STRIP_TRAILING_WHITESPACE)
    if (git_version MATCHES "^git version [0-9]")
      string(REPLACE "git version " "" GIT_VERSION_STRING "${git_version}")
      set_property(GLOBAL PROPERTY _CMAKE_FindGit_GIT_EXECUTABLE_VERSION
        "${GIT_EXECUTABLE};${GIT_VERSION_STRING}"
      )
    endif()
    unset(git_version)
  endif()
  unset(__doGitVersionCheck)

  get_property(_findgit_role GLOBAL PROPERTY CMAKE_ROLE)
  if(_findgit_role STREQUAL "PROJECT" AND NOT TARGET Git::Git)
    add_executable(Git::Git IMPORTED)
    set_property(TARGET Git::Git PROPERTY IMPORTED_LOCATION "${GIT_EXECUTABLE}")
  endif()
  unset(_findgit_role)
endif()

include(${CMAKE_CURRENT_LIST_DIR}/FindPackageHandleStandardArgs.cmake)
find_package_handle_standard_args(Git
                                  REQUIRED_VARS GIT_EXECUTABLE
                                  VERSION_VAR GIT_VERSION_STRING)

```


### FindPackageHandleStandardArgs.cmake
```cmake
# Distributed under the OSI-approved BSD 3-Clause License.  See accompanying
# file Copyright.txt or https://cmake.org/licensing for details.

#[=======================================================================[.rst:
FindPackageHandleStandardArgs
-----------------------------

This module provides functions intended to be used in :ref:`Find Modules`
implementing :command:`find_package(<PackageName>)` calls.

.. command:: find_package_handle_standard_args

  This command handles the ``REQUIRED``, ``QUIET`` and version-related
  arguments of :command:`find_package`.  It also sets the
  ``<PackageName>_FOUND`` variable.  The package is considered found if all
  variables listed contain valid results, e.g. valid filepaths.

  There are two signatures:

  .. code-block:: cmake

    find_package_handle_standard_args(<PackageName>
      (DEFAULT_MSG|<custom-failure-message>)
      <required-var>...
      )

    find_package_handle_standard_args(<PackageName>
      [FOUND_VAR <result-var>]
      [REQUIRED_VARS <required-var>...]
      [VERSION_VAR <version-var>]
      [HANDLE_VERSION_RANGE]
      [HANDLE_COMPONENTS]
      [CONFIG_MODE]
      [NAME_MISMATCHED]
      [REASON_FAILURE_MESSAGE <reason-failure-message>]
      [FAIL_MESSAGE <custom-failure-message>]
      )

  The ``<PackageName>_FOUND`` variable will be set to ``TRUE`` if all
  the variables ``<required-var>...`` are valid and any optional
  constraints are satisfied, and ``FALSE`` otherwise.  A success or
  failure message may be displayed based on the results and on
  whether the ``REQUIRED`` and/or ``QUIET`` option was given to
  the :command:`find_package` call.

  The options are:

  ``(DEFAULT_MSG|<custom-failure-message>)``
    In the simple signature this specifies the failure message.
    Use ``DEFAULT_MSG`` to ask for a default message to be computed
    (recommended).  Not valid in the full signature.

  ``FOUND_VAR <result-var>``
    .. deprecated:: 3.3

    Specifies either ``<PackageName>_FOUND`` or
    ``<PACKAGENAME>_FOUND`` as the result variable.  This exists only
    for compatibility with older versions of CMake and is now ignored.
    Result variables of both names are always set for compatibility.

  ``REQUIRED_VARS <required-var>...``
    Specify the variables which are required for this package.
    These may be named in the generated failure message asking the
    user to set the missing variable values.  Therefore these should
    typically be cache entries such as ``FOO_LIBRARY`` and not output
    variables like ``FOO_LIBRARIES``.

    .. versionchanged:: 3.18
      If ``HANDLE_COMPONENTS`` is specified, this option can be omitted.

  ``VERSION_VAR <version-var>``
    Specify the name of a variable that holds the version of the package
    that has been found.  This version will be checked against the
    (potentially) specified required version given to the
    :command:`find_package` call, including its ``EXACT`` option.
    The default messages include information about the required
    version and the version which has been actually found, both
    if the version is ok or not.

  ``HANDLE_VERSION_RANGE``
    .. versionadded:: 3.19

    Enable handling of a version range, if one is specified. Without this
    option, a developer warning will be displayed if a version range is
    specified.

  ``HANDLE_COMPONENTS``
    Enable handling of package components.  In this case, the command
    will report which components have been found and which are missing,
    and the ``<PackageName>_FOUND`` variable will be set to ``FALSE``
    if any of the required components (i.e. not the ones listed after
    the ``OPTIONAL_COMPONENTS`` option of :command:`find_package`) are
    missing.

  ``CONFIG_MODE``
    Specify that the calling find module is a wrapper around a
    call to ``find_package(<PackageName> NO_MODULE)``.  This implies
    a ``VERSION_VAR`` value of ``<PackageName>_VERSION``.  The command
    will automatically check whether the package configuration file
    was found.

  ``REASON_FAILURE_MESSAGE <reason-failure-message>``
    .. versionadded:: 3.16

    Specify a custom message of the reason for the failure which will be
    appended to the default generated message.

  ``FAIL_MESSAGE <custom-failure-message>``
    Specify a custom failure message instead of using the default
    generated message.  Not recommended.

  ``NAME_MISMATCHED``
    .. versionadded:: 3.17

    Indicate that the ``<PackageName>`` does not match
    ``${CMAKE_FIND_PACKAGE_NAME}``. This is usually a mistake and raises a
    warning, but it may be intentional for usage of the command for components
    of a larger package.

Example for the simple signature:

.. code-block:: cmake

  find_package_handle_standard_args(LibXml2 DEFAULT_MSG
    LIBXML2_LIBRARY LIBXML2_INCLUDE_DIR)

The ``LibXml2`` package is considered to be found if both
``LIBXML2_LIBRARY`` and ``LIBXML2_INCLUDE_DIR`` are valid.
Then also ``LibXml2_FOUND`` is set to ``TRUE``.  If it is not found
and ``REQUIRED`` was used, it fails with a
:command:`message(FATAL_ERROR)`, independent whether ``QUIET`` was
used or not.  If it is found, success will be reported, including
the content of the first ``<required-var>``.  On repeated CMake runs,
the same message will not be printed again.

.. note::

  If ``<PackageName>`` does not match ``CMAKE_FIND_PACKAGE_NAME`` for the
  calling module, a warning that there is a mismatch is given. The
  ``FPHSA_NAME_MISMATCHED`` variable may be set to bypass the warning if using
  the old signature and the ``NAME_MISMATCHED`` argument using the new
  signature. To avoid forcing the caller to require newer versions of CMake for
  usage, the variable's value will be used if defined when the
  ``NAME_MISMATCHED`` argument is not passed for the new signature (but using
  both is an error)..

Example for the full signature:

.. code-block:: cmake

  find_package_handle_standard_args(LibArchive
    REQUIRED_VARS LibArchive_LIBRARY LibArchive_INCLUDE_DIR
    VERSION_VAR LibArchive_VERSION)

In this case, the ``LibArchive`` package is considered to be found if
both ``LibArchive_LIBRARY`` and ``LibArchive_INCLUDE_DIR`` are valid.
Also the version of ``LibArchive`` will be checked by using the version
contained in ``LibArchive_VERSION``.  Since no ``FAIL_MESSAGE`` is given,
the default messages will be printed.

Another example for the full signature:

.. code-block:: cmake

  find_package(Automoc4 QUIET NO_MODULE HINTS /opt/automoc4)
  find_package_handle_standard_args(Automoc4  CONFIG_MODE)

In this case, a ``FindAutmoc4.cmake`` module wraps a call to
``find_package(Automoc4 NO_MODULE)`` and adds an additional search
directory for ``automoc4``.  Then the call to
``find_package_handle_standard_args`` produces a proper success/failure
message.

.. command:: find_package_check_version

  .. versionadded:: 3.19

  Helper function which can be used to check if a ``<version>`` is valid
  against version-related arguments of :command:`find_package`.

  .. code-block:: cmake

    find_package_check_version(<version> <result-var>
      [HANDLE_VERSION_RANGE]
      [RESULT_MESSAGE_VARIABLE <message-var>]
      )

  The ``<result-var>`` will hold a boolean value giving the result of the check.

  The options are:

  ``HANDLE_VERSION_RANGE``
    Enable handling of a version range, if one is specified. Without this
    option, a developer warning will be displayed if a version range is
    specified.

  ``RESULT_MESSAGE_VARIABLE <message-var>``
    Specify a variable to get back a message describing the result of the check.

Example for the usage:

.. code-block:: cmake

  find_package_check_version(1.2.3 result HANDLE_VERSION_RANGE
    RESULT_MESSAGE_VARIABLE reason)
  if (result)
    message (STATUS "${reason}")
  else()
    message (FATAL_ERROR "${reason}")
  endif()
#]=======================================================================]

include(${CMAKE_CURRENT_LIST_DIR}/FindPackageMessage.cmake)


cmake_policy(PUSH)
# numbers and boolean constants
cmake_policy (SET CMP0012 NEW)
# IN_LIST operator
cmake_policy (SET CMP0057 NEW)


# internal helper macro
macro(_FPHSA_FAILURE_MESSAGE _msg)
  set (__msg "${_msg}")
  if (FPHSA_REASON_FAILURE_MESSAGE)
    string(APPEND __msg "\n    Reason given by package: ${FPHSA_REASON_FAILURE_MESSAGE}\n")
  endif()
  if (${_NAME}_FIND_REQUIRED)
    message(FATAL_ERROR "${__msg}")
  else ()
    if (NOT ${_NAME}_FIND_QUIETLY)
      message(STATUS "${__msg}")
    endif ()
  endif ()
endmacro()


# internal helper macro to generate the failure message when used in CONFIG_MODE:
macro(_FPHSA_HANDLE_FAILURE_CONFIG_MODE)
  # <PackageName>_CONFIG is set, but FOUND is false, this means that some other of the REQUIRED_VARS was not found:
  if(${_NAME}_CONFIG)
    _FPHSA_FAILURE_MESSAGE("${FPHSA_FAIL_MESSAGE}: missing:${MISSING_VARS} (found ${${_NAME}_CONFIG} ${VERSION_MSG})")
  else()
    # If _CONSIDERED_CONFIGS is set, the config-file has been found, but no suitable version.
    # List them all in the error message:
    if(${_NAME}_CONSIDERED_CONFIGS)
      set(configsText "")
      list(LENGTH ${_NAME}_CONSIDERED_CONFIGS configsCount)
      math(EXPR configsCount "${configsCount} - 1")
      foreach(currentConfigIndex RANGE ${configsCount})
        list(GET ${_NAME}_CONSIDERED_CONFIGS ${currentConfigIndex} filename)
        list(GET ${_NAME}_CONSIDERED_VERSIONS ${currentConfigIndex} version)
        string(APPEND configsText "\n    ${filename} (version ${version})")
      endforeach()
      if (${_NAME}_NOT_FOUND_MESSAGE)
        if (FPHSA_REASON_FAILURE_MESSAGE)
          string(PREPEND FPHSA_REASON_FAILURE_MESSAGE "${${_NAME}_NOT_FOUND_MESSAGE}\n    ")
        else()
          set(FPHSA_REASON_FAILURE_MESSAGE "${${_NAME}_NOT_FOUND_MESSAGE}")
        endif()
      else()
        string(APPEND configsText "\n")
      endif()
      _FPHSA_FAILURE_MESSAGE("${FPHSA_FAIL_MESSAGE} ${VERSION_MSG}, checked the following files:${configsText}")

    else()
      # Simple case: No Config-file was found at all:
      _FPHSA_FAILURE_MESSAGE("${FPHSA_FAIL_MESSAGE}: found neither ${_NAME}Config.cmake nor ${_NAME_LOWER}-config.cmake ${VERSION_MSG}")
    endif()
  endif()
endmacro()


function(FIND_PACKAGE_CHECK_VERSION version result)
  cmake_parse_arguments (PARSE_ARGV 2 FPCV "HANDLE_VERSION_RANGE;NO_AUTHOR_WARNING_VERSION_RANGE" "RESULT_MESSAGE_VARIABLE" "")

  if (FPCV_UNPARSED_ARGUMENTS)
    message (FATAL_ERROR "find_package_check_version(): ${FPCV_UNPARSED_ARGUMENTS}: unexpected arguments")
  endif()
  if ("RESULT_MESSAGE_VARIABLE" IN_LIST FPCV_KEYWORDS_MISSING_VALUES)
    message (FATAL_ERROR "find_package_check_version(): RESULT_MESSAGE_VARIABLE expects an argument")
  endif()

  set (${result} FALSE PARENT_SCOPE)
  if (FPCV_RESULT_MESSAGE_VARIABLE)
    unset (${FPCV_RESULT_MESSAGE_VARIABLE} PARENT_SCOPE)
  endif()

  if (_CMAKE_FPHSA_PACKAGE_NAME)
    set (package "${_CMAKE_FPHSA_PACKAGE_NAME}")
  elseif (CMAKE_FIND_PACKAGE_NAME)
    set (package "${CMAKE_FIND_PACKAGE_NAME}")
  else()
    message (FATAL_ERROR "find_package_check_version(): Cannot be used outside a 'Find Module'")
  endif()

  if (NOT FPCV_NO_AUTHOR_WARNING_VERSION_RANGE
      AND ${package}_FIND_VERSION_RANGE AND NOT FPCV_HANDLE_VERSION_RANGE)
    message(AUTHOR_WARNING
      "`find_package()` specify a version range but the option "
      "HANDLE_VERSION_RANGE` is not passed to `find_package_check_version()`. "
      "Only the lower endpoint of the range will be used.")
  endif()


  set (version_ok FALSE)
  unset (version_msg)

  if (FPCV_HANDLE_VERSION_RANGE AND ${package}_FIND_VERSION_RANGE)
    if ((${package}_FIND_VERSION_RANGE_MIN STREQUAL "INCLUDE"
          AND version VERSION_GREATER_EQUAL ${package}_FIND_VERSION_MIN)
        AND ((${package}_FIND_VERSION_RANGE_MAX STREQUAL "INCLUDE"
            AND version VERSION_LESS_EQUAL ${package}_FIND_VERSION_MAX)
          OR (${package}_FIND_VERSION_RANGE_MAX STREQUAL "EXCLUDE"
            AND version VERSION_LESS ${package}_FIND_VERSION_MAX)))
      set (version_ok TRUE)
      set(version_msg "(found suitable version \"${version}\", required range is \"${${package}_FIND_VERSION_RANGE}\")")
    else()
      set(version_msg "Found unsuitable version \"${version}\", required range is \"${${package}_FIND_VERSION_RANGE}\"")
    endif()
  elseif (DEFINED ${package}_FIND_VERSION)
    if(${package}_FIND_VERSION_EXACT)       # exact version required
      # count the dots in the version string
      string(REGEX REPLACE "[^.]" "" version_dots "${version}")
      # add one dot because there is one dot more than there are components
      string(LENGTH "${version_dots}." version_dots)
      if (version_dots GREATER ${package}_FIND_VERSION_COUNT)
        # Because of the C++ implementation of find_package() ${package}_FIND_VERSION_COUNT
        # is at most 4 here. Therefore a simple lookup table is used.
        if (${package}_FIND_VERSION_COUNT EQUAL 1)
          set(version_regex "[^.]*")
        elseif (${package}_FIND_VERSION_COUNT EQUAL 2)
          set(version_regex "[^.]*\\.[^.]*")
        elseif (${package}_FIND_VERSION_COUNT EQUAL 3)
          set(version_regex "[^.]*\\.[^.]*\\.[^.]*")
        else()
          set(version_regex "[^.]*\\.[^.]*\\.[^.]*\\.[^.]*")
        endif()
        string(REGEX REPLACE "^(${version_regex})\\..*" "\\1" version_head "${version}")
        if (NOT ${package}_FIND_VERSION VERSION_EQUAL version_head)
          set(version_msg "Found unsuitable version \"${version}\", but required is exact version \"${${package}_FIND_VERSION}\"")
        else ()
          set(version_ok TRUE)
          set(version_msg "(found suitable exact version \"${_FOUND_VERSION}\")")
        endif ()
      else ()
        if (NOT ${package}_FIND_VERSION VERSION_EQUAL version)
          set(version_msg "Found unsuitable version \"${version}\", but required is exact version \"${${package}_FIND_VERSION}\"")
        else ()
          set(version_ok TRUE)
          set(version_msg "(found suitable exact version \"${version}\")")
        endif ()
      endif ()
    else()     # minimum version
      if (${package}_FIND_VERSION VERSION_GREATER version)
        set(version_msg "Found unsuitable version \"${version}\", but required is at least \"${${package}_FIND_VERSION}\"")
      else()
        set(version_ok TRUE)
        set(version_msg "(found suitable version \"${version}\", minimum required is \"${${package}_FIND_VERSION}\")")
      endif()
    endif()
  else ()
    set(version_ok TRUE)
    set(version_msg "(found version \"${version}\")")
  endif()

  set (${result} ${version_ok} PARENT_SCOPE)
  if (FPCV_RESULT_MESSAGE_VARIABLE)
    set (${FPCV_RESULT_MESSAGE_VARIABLE} "${version_msg}" PARENT_SCOPE)
  endif()
endfunction()


function(FIND_PACKAGE_HANDLE_STANDARD_ARGS _NAME _FIRST_ARG)

  # Set up the arguments for `cmake_parse_arguments`.
  set(options  CONFIG_MODE  HANDLE_COMPONENTS NAME_MISMATCHED HANDLE_VERSION_RANGE)
  set(oneValueArgs  FAIL_MESSAGE  REASON_FAILURE_MESSAGE VERSION_VAR  FOUND_VAR)
  set(multiValueArgs REQUIRED_VARS)

  # Check whether we are in 'simple' or 'extended' mode:
  set(_KEYWORDS_FOR_EXTENDED_MODE  ${options} ${oneValueArgs} ${multiValueArgs} )
  list(FIND _KEYWORDS_FOR_EXTENDED_MODE "${_FIRST_ARG}" INDEX)

  unset(FPHSA_NAME_MISMATCHED_override)
  if (DEFINED FPHSA_NAME_MISMATCHED)
    # If the variable NAME_MISMATCHED variable is set, error if it is passed as
    # an argument. The former is for old signatures, the latter is for new
    # signatures.
    list(FIND ARGN "NAME_MISMATCHED" name_mismatched_idx)
    if (NOT name_mismatched_idx EQUAL "-1")
      message(FATAL_ERROR
        "The `NAME_MISMATCHED` argument may only be specified by the argument or "
        "the variable, not both.")
    endif ()

    # But use the variable if it is not an argument to avoid forcing minimum
    # CMake version bumps for calling modules.
    set(FPHSA_NAME_MISMATCHED_override "${FPHSA_NAME_MISMATCHED}")
  endif ()

  if(${INDEX} EQUAL -1)
    set(FPHSA_FAIL_MESSAGE ${_FIRST_ARG})
    set(FPHSA_REQUIRED_VARS ${ARGN})
    set(FPHSA_VERSION_VAR)
  else()
    cmake_parse_arguments(FPHSA "${options}" "${oneValueArgs}" "${multiValueArgs}"  ${_FIRST_ARG} ${ARGN})

    if(FPHSA_UNPARSED_ARGUMENTS)
      message(FATAL_ERROR "Unknown keywords given to FIND_PACKAGE_HANDLE_STANDARD_ARGS(): \"${FPHSA_UNPARSED_ARGUMENTS}\"")
    endif()

    if(NOT FPHSA_FAIL_MESSAGE)
      set(FPHSA_FAIL_MESSAGE  "DEFAULT_MSG")
    endif()

    # In config-mode, we rely on the variable <PackageName>_CONFIG, which is set by find_package()
    # when it successfully found the config-file, including version checking:
    if(FPHSA_CONFIG_MODE)
      list(INSERT FPHSA_REQUIRED_VARS 0 ${_NAME}_CONFIG)
      list(REMOVE_DUPLICATES FPHSA_REQUIRED_VARS)
      set(FPHSA_VERSION_VAR ${_NAME}_VERSION)
    endif()

    if(NOT FPHSA_REQUIRED_VARS AND NOT FPHSA_HANDLE_COMPONENTS)
      message(FATAL_ERROR "No REQUIRED_VARS specified for FIND_PACKAGE_HANDLE_STANDARD_ARGS()")
    endif()
  endif()

  if (DEFINED FPHSA_NAME_MISMATCHED_override)
    set(FPHSA_NAME_MISMATCHED "${FPHSA_NAME_MISMATCHED_override}")
  endif ()

  if (DEFINED CMAKE_FIND_PACKAGE_NAME
      AND NOT FPHSA_NAME_MISMATCHED
      AND NOT _NAME STREQUAL CMAKE_FIND_PACKAGE_NAME)
    message(AUTHOR_WARNING
      "The package name passed to `find_package_handle_standard_args` "
      "(${_NAME}) does not match the name of the calling package "
      "(${CMAKE_FIND_PACKAGE_NAME}). This can lead to problems in calling "
      "code that expects `find_package` result variables (e.g., `_FOUND`) "
      "to follow a certain pattern.")
  endif ()

  if (${_NAME}_FIND_VERSION_RANGE AND NOT FPHSA_HANDLE_VERSION_RANGE)
    message(AUTHOR_WARNING
      "`find_package()` specify a version range but the module ${_NAME} does "
      "not support this capability. Only the lower endpoint of the range "
      "will be used.")
  endif()

  # to propagate package name to FIND_PACKAGE_CHECK_VERSION
  set(_CMAKE_FPHSA_PACKAGE_NAME "${_NAME}")

  # now that we collected all arguments, process them

  if("x${FPHSA_FAIL_MESSAGE}" STREQUAL "xDEFAULT_MSG")
    set(FPHSA_FAIL_MESSAGE "Could NOT find ${_NAME}")
  endif()

  if (FPHSA_REQUIRED_VARS)
    list(GET FPHSA_REQUIRED_VARS 0 _FIRST_REQUIRED_VAR)
  endif()

  string(TOUPPER ${_NAME} _NAME_UPPER)
  string(TOLOWER ${_NAME} _NAME_LOWER)

  if(FPHSA_FOUND_VAR)
    set(_FOUND_VAR_UPPER ${_NAME_UPPER}_FOUND)
    set(_FOUND_VAR_MIXED ${_NAME}_FOUND)
    if(FPHSA_FOUND_VAR STREQUAL _FOUND_VAR_MIXED  OR  FPHSA_FOUND_VAR STREQUAL _FOUND_VAR_UPPER)
      set(_FOUND_VAR ${FPHSA_FOUND_VAR})
    else()
      message(FATAL_ERROR "The argument for FOUND_VAR is \"${FPHSA_FOUND_VAR}\", but only \"${_FOUND_VAR_MIXED}\" and \"${_FOUND_VAR_UPPER}\" are valid names.")
    endif()
  else()
    set(_FOUND_VAR ${_NAME_UPPER}_FOUND)
  endif()

  # collect all variables which were not found, so they can be printed, so the
  # user knows better what went wrong (#6375)
  set(MISSING_VARS "")
  set(DETAILS "")
  # check if all passed variables are valid
  set(FPHSA_FOUND_${_NAME} TRUE)
  foreach(_CURRENT_VAR ${FPHSA_REQUIRED_VARS})
    if(NOT ${_CURRENT_VAR})
      set(FPHSA_FOUND_${_NAME} FALSE)
      string(APPEND MISSING_VARS " ${_CURRENT_VAR}")
    else()
      string(APPEND DETAILS "[${${_CURRENT_VAR}}]")
    endif()
  endforeach()
  if(FPHSA_FOUND_${_NAME})
    set(${_NAME}_FOUND TRUE)
    set(${_NAME_UPPER}_FOUND TRUE)
  else()
    set(${_NAME}_FOUND FALSE)
    set(${_NAME_UPPER}_FOUND FALSE)
  endif()

  # component handling
  unset(FOUND_COMPONENTS_MSG)
  unset(MISSING_COMPONENTS_MSG)

  if(FPHSA_HANDLE_COMPONENTS)
    foreach(comp ${${_NAME}_FIND_COMPONENTS})
      if(${_NAME}_${comp}_FOUND)

        if(NOT DEFINED FOUND_COMPONENTS_MSG)
          set(FOUND_COMPONENTS_MSG "found components:")
        endif()
        string(APPEND FOUND_COMPONENTS_MSG " ${comp}")

      else()

        if(NOT DEFINED MISSING_COMPONENTS_MSG)
          set(MISSING_COMPONENTS_MSG "missing components:")
        endif()
        string(APPEND MISSING_COMPONENTS_MSG " ${comp}")

        if(${_NAME}_FIND_REQUIRED_${comp})
          set(${_NAME}_FOUND FALSE)
          string(APPEND MISSING_VARS " ${comp}")
        endif()

      endif()
    endforeach()
    set(COMPONENT_MSG "${FOUND_COMPONENTS_MSG} ${MISSING_COMPONENTS_MSG}")
    string(APPEND DETAILS "[c${COMPONENT_MSG}]")
  endif()

  # version handling:
  set(VERSION_MSG "")
  set(VERSION_OK TRUE)

  # check that the version variable is not empty to avoid emitting a misleading
  # message (i.e. `Found unsuitable version ""`)
  if (DEFINED ${_NAME}_FIND_VERSION)
    if(DEFINED ${FPHSA_VERSION_VAR})
      if(NOT "${${FPHSA_VERSION_VAR}}" STREQUAL "")
        set(_FOUND_VERSION ${${FPHSA_VERSION_VAR}})
        if (FPHSA_HANDLE_VERSION_RANGE)
          set (FPCV_HANDLE_VERSION_RANGE HANDLE_VERSION_RANGE)
        else()
          set(FPCV_HANDLE_VERSION_RANGE NO_AUTHOR_WARNING_VERSION_RANGE)
        endif()
        find_package_check_version ("${_FOUND_VERSION}" VERSION_OK RESULT_MESSAGE_VARIABLE VERSION_MSG
          ${FPCV_HANDLE_VERSION_RANGE})
      else()
        set(VERSION_OK FALSE)
      endif()
    endif()
    if("${${FPHSA_VERSION_VAR}}" STREQUAL "")
      # if the package was not found, but a version was given, add that to the output:
      if(${_NAME}_FIND_VERSION_EXACT)
        set(VERSION_MSG "(Required is exact version \"${${_NAME}_FIND_VERSION}\")")
      elseif (FPHSA_HANDLE_VERSION_RANGE AND ${_NAME}_FIND_VERSION_RANGE)
        set(VERSION_MSG "(Required is version range \"${${_NAME}_FIND_VERSION_RANGE}\")")
      else()
        set(VERSION_MSG "(Required is at least version \"${${_NAME}_FIND_VERSION}\")")
      endif()
    endif()
  else ()
    # Check with DEFINED as the found version may be 0.
    if(DEFINED ${FPHSA_VERSION_VAR})
      set(VERSION_MSG "(found version \"${${FPHSA_VERSION_VAR}}\")")
    endif()
  endif ()

  if(VERSION_OK)
    string(APPEND DETAILS "[v${${FPHSA_VERSION_VAR}}(${${_NAME}_FIND_VERSION})]")
  else()
    set(${_NAME}_FOUND FALSE)
  endif()


  # print the result:
  if (${_NAME}_FOUND)
    FIND_PACKAGE_MESSAGE(${_NAME} "Found ${_NAME}: ${${_FIRST_REQUIRED_VAR}} ${VERSION_MSG} ${COMPONENT_MSG}" "${DETAILS}")
  else ()

    if(FPHSA_CONFIG_MODE)
      _FPHSA_HANDLE_FAILURE_CONFIG_MODE()
    else()
      if(NOT VERSION_OK)
        set(RESULT_MSG)
        if (_FIRST_REQUIRED_VAR)
          string (APPEND RESULT_MSG "found ${${_FIRST_REQUIRED_VAR}}")
        endif()
        if (COMPONENT_MSG)
          if (RESULT_MSG)
            string (APPEND RESULT_MSG ", ")
          endif()
          string (APPEND RESULT_MSG "${FOUND_COMPONENTS_MSG}")
        endif()
        _FPHSA_FAILURE_MESSAGE("${FPHSA_FAIL_MESSAGE}: ${VERSION_MSG} (${RESULT_MSG})")
      else()
        _FPHSA_FAILURE_MESSAGE("${FPHSA_FAIL_MESSAGE} (missing:${MISSING_VARS}) ${VERSION_MSG}")
      endif()
    endif()

  endif ()

  set(${_NAME}_FOUND ${${_NAME}_FOUND} PARENT_SCOPE)
  set(${_NAME_UPPER}_FOUND ${${_NAME}_FOUND} PARENT_SCOPE)
endfunction()


cmake_policy(POP)


```


### FindPackageMessage.cmake
```cmake
# Distributed under the OSI-approved BSD 3-Clause License.  See accompanying
# file Copyright.txt or https://cmake.org/licensing for details.

#[=======================================================================[.rst:
FindPackageMessage
------------------

.. code-block:: cmake

  find_package_message(<name> "message for user" "find result details")

This function is intended to be used in FindXXX.cmake modules files.
It will print a message once for each unique find result.  This is
useful for telling the user where a package was found.  The first
argument specifies the name (XXX) of the package.  The second argument
specifies the message to display.  The third argument lists details
about the find result so that if they change the message will be
displayed again.  The macro also obeys the QUIET argument to the
find_package command.

Example:

.. code-block:: cmake

  if(X11_FOUND)
    find_package_message(X11 "Found X11: ${X11_X11_LIB}"
      "[${X11_X11_LIB}][${X11_INCLUDE_DIR}]")
  else()
   ...
  endif()
#]=======================================================================]

function(find_package_message pkg msg details)
  # Avoid printing a message repeatedly for the same find result.
  if(NOT ${pkg}_FIND_QUIETLY)
    string(REPLACE "\n" "" details "${details}")
    set(DETAILS_VAR FIND_PACKAGE_MESSAGE_DETAILS_${pkg})
    if(NOT "${details}" STREQUAL "${${DETAILS_VAR}}")
      # The message has not yet been printed.
      message(STATUS "${msg}")

      # Save the find details in the cache to avoid printing the same
      # message again.
      set("${DETAILS_VAR}" "${details}"
        CACHE INTERNAL "Details about finding ${pkg}")
    endif()
  endif()
endfunction()


```







