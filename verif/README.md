# Verification Documentation
The sub-directories below this point contain all of the verification documentation for the CORE-V family of RISC-V cores.
* **Common**: CORE-V verification documentation not specific to any one core
* **CV32E40P**: verification documentation specific to the CV32E40P core
* **CV32E40**: verification documentation specific to the CV32E40 core
# How to Write a Verification Plan (Testplan)
The CORE-V projects use spreadsheets to capture Testplans.  I know, I know, we _all_ hate spreadsheets, but they really are the best format for this type of data.  The template for the spreadsheet is simple enough that you can use either Microsoft Office Excel or LibreOffice Calc.  The key is to start with the template from the **Common** directory.
## Verification Planning
A key activity of any verification effort is to capture a Verification Plan (aka Test Plan or just testplan).  The purpose of a verification plan is to identify what features need to be verified; the success criteria of the feature and the coverage metrics for testing the feature.

A Verification Plan should focus on the **_what_**, and not the **_how_** of verification.  When capturing a testplan we are mostly interested in creating a laundry list of thing to verify.  At this stage we are not (yet) concerned with how to verify them.

The “how” part is captured in the Verification Strategy document.  That document exists to support the Verification Plan. For example the CV32E40P testplan specifies that all RV32I instructions be generated and their results checked.  Obviously, the testbench needs to have these capabilities and its the purpose of the Verification Strategy document to explain how that is done.
## A Trivial Example: the RV32I ADDI Instruction
Let's assume your task is to verify the CV32E40P's implementation of the RV32I ADDI instruction.  Simple right?  Create a simple assembler program with a few **_addi_** instructions check the results and we're done.  Of course, checking for the correct result (rd = rs1 + imm), is insufficent.  We also need to check:
* Overflow is detected and flagged correctly
* Underflow is detected and flagged correctly
* No instruction execution side-effects (e.g. unexpected GPR changes, unexpected condition codes)

Its also important that the instruction is fully exercised, so we also need to cover the following cases:
* Use x0..x31 as rs1
* Use x0..x31 as rd (Note: the result of this operation will always be 0x00000000 when rd is x0)
* Set/Clear all bits of immediate
* Set/Clear all bits of rs1
* Set/Clear all bits of rd

Note the simplifying assumptions made here.  With one 32-bit and one 12-bit operand there are 2,244 unique sums that can be calculated.  Including the cross-products of source and destination register yields O(10^6) unique instruction calls.  The RV32I ISA specifies 40 instructions so this gives us O(10^7) instruction executions simply to fully verify the most basic instructions in a CORE-V design.  Obviously this is impractical and one of the things that makes Verification an Art is determing the minimal amount of coverage to have confidence that a feature is sufficiently tested.  It is the opinion of the author that the above coverage is sufficient for the addi instruction.  You may see it as overkill or underkill depending on your understanding of the micro-architecture or your risk adversion.

So, specifying the Testplan for the addi instruction forces us to think about what the feature-under-test does, what we need to check to ensure its done properly and what stimulus and configuration needs to be covered to ensure the feature is tested under all penitent conditions.

The template used for this project attempts to provide an easy-to-use format to capture and review this information for every feature in the design.
## HOWTO: The CORE-V Verification Plan Template
The following sub-sections explain each of the columns in the [template spreadsheet](https://github.com/openhwgroup/core-v-docs/blob/master/verif/CV32E40P/VerificationPlan/CORE-V_VerifPlan_Template.xlsx).
### Requirement Location
This is a pointer to the source Requirements document of the Features in question.  It can be a standards document, such as the RISC-V ISA, or a micro-architecture specification.   Please include a chapter or section number.
### Feature
The high-level feature you are trying to verify.  For example, RV32I Register-Immediate Instructions.
### Sub-Feature
This is an optional, but often used column.  Using our previous examples, ADDI is a sub-feature of RV32I Register-Immediate Instructions.  If it makes sense to decompose the Feature into two or more sub-features, use this columnn for that.  If required, add a column for sub-sub-features. 
### Feature Description
A summary of what the features does.  It should be a _summary_, not a verbatium copy-n-paste from the Requirements Document.
### Verification Goals
A summary of what stimulus and/or configuration needs to be generated/checked/covered to ensue sufficient testing of the Feature.
### Pass/Fail Criteria
How will the testbench know the test passed?
### Test Type
Specify whether the Feature will be covered in a Compliance test, a manually written Directed test or a Constrained-Random test.
### Coverage Method
How will we know that the Feature is verified (covered)?  There are several choices here:
* **Testcase:** if the testcase was run, the Feature was tested.
* **Functional Coverage:** the testbench supports explicit functional coverage to measure the required stimulus/configuration conditions to show that the Feature was tested.  **This is the perferred method of coverage.**
* **Code Coverage:** the Feature is deemed to be tested when the specific conditions in the RTL have been exercised.
### Link to Coverage
This field is used to link the Feature to coverage data generated in Regression.  Leave this blank for now as this information is tool dependent.
