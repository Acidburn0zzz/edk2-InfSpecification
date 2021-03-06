<!--- @file
  3.15 [Binaries] Section

  Copyright (c) 2007-2017, Intel Corporation. All rights reserved.<BR>

  Redistribution and use in source (original document form) and 'compiled'
  forms (converted to PDF, epub, HTML and other formats) with or without
  modification, are permitted provided that the following conditions are met:

  1) Redistributions of source code (original document form) must retain the
     above copyright notice, this list of conditions and the following
     disclaimer as the first lines of this file unmodified.

  2) Redistributions in compiled form (transformed to other DTDs, converted to
     PDF, epub, HTML and other formats) must reproduce the above copyright
     notice, this list of conditions and the following disclaimer in the
     documentation and/or other materials provided with the distribution.

  THIS DOCUMENTATION IS PROVIDED BY TIANOCORE PROJECT "AS IS" AND ANY EXPRESS OR
  IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF
  MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO
  EVENT SHALL TIANOCORE PROJECT  BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
  SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO,
  PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS;
  OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY,
  WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR
  OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS DOCUMENTATION, EVEN IF
  ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

-->

## 3.15 [Binaries] Section

#### Summary

Defines the `[Binaries]` tag is required for EDK II INF files for Binary
Modules.

This is a required section for Binary Modules Only.

Each binary file must be listed only once per section. Files listed in
architectural sections are not permitted to be listed in the common
architectural section.

The "common" architecture modifier in a section tag must not be combined with
other architecture type; doing so will result in a build break.

There can be multiple `[Binaries]` sections, depending on the target processor.
Example binaries sections are listed below. Each binary file's path is relative
to the location of the component's INF file. The parsing utility creates a
directory path for each file (`$(DEST_DIR)/Path/OUTPUT`), and copies each file
(or a processed version of a Unicode User Interface or Version section) to the
`OUTPUT` directory. No makefile is produced, as binary files are only used by
the third phase of a build, creating FV, FD or similar binary files.

All file names specified in this section must be in the directory containing
the INF file or in sub-directories of the directory containing the INF file.

When a binary INF file is generated by tools during a source build, if a symbol
file, such as a PDB or SYM file is generated, the tools must add the file to
this section using a binary file type of "DISPOSABLE".

One and only one `EFI_SECTION_VERSION` is allowed in a FFS image, therefore one
and only one `VER` (`VER` or `UNI_VER`) can be included in any one `[Binaries]`
section.

One and only one `EFI_SECTION_USER_INTERFACE` is allowed in a FFS image,
therefore one and only one `UI` (`UI` or `UNI_UI`) can be included in any one
`[Binaries]` section.

One and only one `EFI_SECTION_FREEFORM_SUBTYPE_GUID` is allowed in a FFS image,
therefore one and only one `SUBTYPE_GUID` with a unique GUID Value can be
included in any one `[Binaries]` section.

#### Prototype

```c
<Binaries>           ::= "[Binaries" [<com_attribs>] "]" <EOL>
                         [<UiExpression>]
                         [<VerExpression>]
                         <BinariesStatements>*
<BinariesStatements> ::= {<MacroDefinition>} {<BinaryFiles>} {<SubTypeGuid>}
<com_attribs>        ::= {".common"} {<attribs>}
<attribs>            ::= <attrs> ["," <TS> "Binaries" <attrs>]*
<attrs>              ::= "." <arch>
<UiExpression>       ::= <TS> <UiFile> [<UiOptions>] <EOL>
<UiOptions>          ::= <FS> [<Target>] [<FS> <FeatureFlagExpress>]
<UiFile>             ::= <UiType> <FS> <Filename>
<UiType>             ::= {"UNI_UI"} {"UI"}
<VerExpression>      ::= <TS> <VerFile> [<VerOptions>] <EOL>
<VerOptions>         ::= <FS> [<Target>] [<FS> <FeatureFlagExpress>]
<VerFile>            ::= <VerType> <FS> <Filename>
<VerType>            ::= {"UNI_VER"} {"VER"}
<SubTypeGuid>        ::= <TS> "SUBTYPE_GUID" <GuidOpts> <EOL>
<GuidOpts>           ::= <FS> <GuidValue> <FileOpts> <EOL>
<GuidValue>          ::= {<CName>} {<RegistryFormatGUID>}
<BinaryFiles>        ::= <TS> <FileType> <FileOpts> <EOL>
<FileOpts>           ::= <FS> <Filename> [<Options>] <EOL>
<Options>            ::= <FS> [<Target>] [<Opt1>]
<Opt1>               ::= <FS> [<Family>] [<Opt2>]
<Opt2>               ::= <FS> [<TagName>] [<Opt3>]
<Opt3>               ::= <FS> <FeatureFlagExpress>
<Target>             ::= {<ToolWord>} {<Wildcard>}
<Family>             ::= {"MSFT"} {"GCC"} {"INTEL"} {<Usr>}
                         {<Wildcard>}
<Usr>                ::= <ToolWord>
<TagName>            ::= {ToolWord} {<Wildcard>}
<FeatureFlagExpress> ::= <Boolean>
<FileType>           ::= <Edk2FileType>
<Edk2FileType>       ::= {"ACPI"} {"ASL"} {"PE32"} {"PIC"} {"FV"}
                         {"PEI_DEPEX"} {"DXE_DEPEX"} {"SMM_DEPEX"}
                         {"TE"} {"BIN"} {"RAW"} {"COMPAT16"}
                         {"DISPOSABLE"} {"LIB"}
```

#### Parameters

**_FeatureFlagExpress_**

When present, the feature flag expression determines whether the entry line is
valid. If the feature flag expression evaluates to FALSE, this entry will be
ignored by the EDK II build tools.

**********
**Note:** For more information about the following parameters, refer to the
Build Specification for a description of the tools_def.txt file. In order for
the entries in the INF file to be valid, there must be a matching definition
in the tools_def.txt file. The tool chain tag name must also match the one
used for the build.
**********

**_Target_**

A keyword that uniquely identifies the build target. This keyword is used to
bind command flags to individual commands. Refer to the Build Specification for
the exact format of the `tools_def.txt` file. The `tools_def.txt` file defines
a label to specify different items such as executables, options and locations.
The label is broken up into fields which are separated by an underscore
character. The Target must be either a wildcard character (meaning all targets)
or it must be specified in the first field of at least one of these labels.
Three values, "NOOPT", "DEBUG" and "RELEASE", have been pre-defined.

Users may want to add other definitions, such as, PERF, SIZE or SPEED, and
define their own set of FLAGS to use with these tags.

**_TagName_**

TagNames must also appear in at least one Label specified in the
`tools_def.txt` file. The `TagName` must be either a wildcard character
(meaning any TagName) or it must match a defined value for a `TagName` label in
the `tools_def.txt` file for the tool chain tag name specified.

**_Family_**

`Family` is keyword that uniquely identifies a tool chain family. The `Family`
must be either a wildcard character (meaning any `Family`) or it must match a
defined value for a `Family` label in the `tools_def.txt` file for at least one
tool chain `TagName` specified in `tools_def.txt` (or the `TagName` field that
follows this field in the entry).

**_FileType: "SUBTYPE_GUID"_**

The file type, "SUBTYPE_GUID" is shorthand for the
`EFI_FREEFORM_SUBTYPE_GUID_SECTION` section.

**_FileType: "DISPOSABLE"_**

The file type, "DISPOSABLE" does not represent content for the
`EFI_SECTION_DISPOSABLE`. These files will not be processed by EDK II build
tools, but rather, may specify other types of files that may be used such as
PDB or SYMS files generated for symbolic debugging.

#### Example

```ini
[Binaries.common]
  UNI_UI|DxeIpl.ui
  UNI_VER|DxeLoad.ver

[Binaries.Ia32]
  DXE_DEPEX|Release/DxeIpl.dpx            # MYTOOLS
  PE32|Debug/Ia32/DxeIpl.efi|DEBUG        # MYTOOLS
  PE32|Release/Ia32/DxeIpl.efi|RELEASE    # MYTOOLS
  DISPOSABLE|Debug/Ia32/DxeIpl.pdb|DEBUG

[Binaries.X64]
  DXE_DEPEX|Debug/X64/DxeIpl.dpx          # MYTOOLS
  PE32|Debug/X64/DxeIpl.efi|DEBUG         # MYTOOLS
  DISPOSABLE|Debug/X64/DxeIpl.pdb|DEBUG

[Binaries.IPF]
  DXE_DEPEX|Debug/IPF/DxeIpl.dpx          # MYTOOLS
  PE32|Debug/Ipf/DxeIpl.efi|DEBUG         # MYTOOLS
  DISPOSABLE|Debug/Ipf/DxeIpl.pdb|DEBUG
```
