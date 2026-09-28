; =======================================================================
; ---------- ASSEMBLY GHOSTS CORE ARCHITECTURAL REGISTER DEFINES --------
; =======================================================================

;this is the MSRS of Intel and AMD 

;========================================================================
; Part 1 - Shared MSRS hypervisor AMD and Intel:
;========================================================================

; =======================================================================
; 1. CORE ARCHITECTURAL REGISTERS (Identical on both Intel & AMD)
; =======================================================================
%define EFER_MSR            0xC0000080  ; Extended Feature Enable Register (NXE, SVME, LME)
%define IA32_STAR           0xC0000081  ; Legacy Ring 0/3 Segment Selectors Target Map for SYSCALL/SYSRET
%define IA32_LSTAR          0xC0000082  ; 64-bit Target RIP Vector for execution entry on SYSCALL instruction
%define IA32_CSTAR          0xC0000083  ; Compatibility Mode Target RIP Vector for SYSCALL execution
%define IA32_FMASK          0xC0000084  ; RFLAGS Bitmask to clear dynamically during SYSCALL transition
%define IA32_FS_BASE        0xC0000100  ; Linear base target physical mapping address for FS register
%define IA32_GS_BASE        0xC0000101  ; Linear base target physical mapping address for GS register
%define IA32_KERNEL_GS_BASE 0xC0000102  ; Swapped base target mapping address for OS Kernel via SWAPGS
%define IA32_TSC_AUX        0xC0000103  ; Auxiliary Time Stamp Counter (Used by RDTSCP instruction)

; =======================================================================
; 2. SIDE-CHANNEL ATTACK MITIGATIONS & SILICON ISOLATION SHIELDS
; =======================================================================
%define IA32_SPEC_CTRL      0x00000048  ; Speculative Execution Control (IBRS, STIBP, SSBD switches)
%define IA32_PRED_CMD       0x00000049  ; Prediction Command Trigger (IBPB branch history flush)
%define IA32_PRED_CTRL      0x0000004C  ; Prediction Control Register (Enforces hardware-level branch isolation)
%define IA32_FLUSH_CMD      0x0000010B  ; L1 Data Cache Hard Flush Command Trigger (Meltdown shield)
%define IA32_ARCH_CAPABILITIES 0x0000010A ; Architectural Capabilities (Read rogue data mitigation flags)
%define IA32_CORE_CAPABILITIES 0x000000CF ; Architectural Processor Core Capabilities Frame

; =======================================================================
; 3. CORE MEMORY TYPING, RANGE REGISTERS (MTRRs) & SYSTEM APIC
; =======================================================================
%define IA32_APIC_BASE      0x0000001B  ; Local APIC Base Physical Address and Enable/Disable register
%define IA32_MTRR_DEF_TYPE  0x000002FF  ; Memory Type Range Register Default Memory Access Type Frame
%define MSR_MTRRfix64k_00000 0x00000250 ; Fixed-Range MTRR mapping for low 64KB physical address block
%define MSR_MTRRcap         0x000000FE  ; MTRR Architectural Capabilities (Read supported memory types)

; =======================================================================
; 4. CONTROL-FLOW ENFORCEMENT TECHNOLOGY (CET ANTI-ROP/ANTI-JOP)
; =======================================================================
%define IA32_U_CET          0x000006A0  ; Control-flow Enforcement Register - User Mode Restrictions
%define IA32_S_CET          0x000006A1  ; Control-flow Enforcement Register - Supervisor Kernel Mode
%define IA32_PL0_SSP        0x000006A4  ; Privilege Level 0 Shadow Stack Pointer Context Register
%define IA32_INTERRUPT_SSP_TABLE_ADDR 0x000006A8 ; Shadow Stack Pointer table address for hardware interrupts

; =======================================================================
; 5. PERFORMANCE MONITORING & ARCHITECTURAL DEBUGGING
; =======================================================================
%define IA32_PERF_GLOBAL_CTRL 0x0000038F ; Global Performance Counter Execution Master Control Switch
%define IA32_PERF_STATUS    0x00000198  ; Silicon Operating Frequency and Voltage Status Register
%define IA32_DEBUGCTL       0x000001D9  ; Hardware Debug Control Interface (LBR, BTF, Branch Recording)
%define MSR_BR_FROM_IP      0x00000680  ; Last Branch Record (LBR) Source Execution Instruction Pointer
%define MSR_BR_TO_IP        0x000006C0  ; Last Branch Record (LBR) Destination Execution Target Vector

; =======================================================================
; 6. THERMAL PATROL, TEMPERATURE MANAGEMENT & ENHANCED POWER CONTROL
; =======================================================================
%define IA32_THERM_CONTROL  0x0000019A  ; Clock Modulation and Silicon Thermal Monitor Interface Mask
%define IA32_THERM_STATUS   0x0000019C  ; Digital Temperature Sensor Output and Max Limit Thresholds
%define IA32_MISC_ENABLE    0x000001A0  ; Miscellaneous Processor Features Enable Frame (XD, SpeedStep)
%define IA32_ENERGY_PERF_BIAS 0x000001B1 ; Hardware Energy Policy Optimization Preference Configuration
%define IA32_UMWAIT_CONTROL 0x000000E1  ; User Mode Wait Control MSR

; =======================================================================
; 7. TLB HARDWARE MANAGEMENT & PROCESSOR CORE ISOLATION
; =======================================================================
%define IA32_PASID          0x000000D0  ; Process Address Space ID MSR (Enforces safe hardware context separation)
%define IA32_FLUSH_TLB      0x0000010E  ; Hardware Direct TLB Invalidation Trigger Register (Cache cleaner)

; =======================================================================
; 8. TIME STAMP COUNTER (TSC) & ACCURATE TIMING CONTROL
; =======================================================================
%define IA32_TIME_STAMP_COUNTER 0x00000010 ; Raw Hardware Cycle Counter (Target for TSC manipulation)
%define IA32_TSC_ADJUST     0x0000003B  ; TSC Offset Adjustment Frame (Used to hide hypervisor latency)
%define IA32_TSC_DEADLINE   0x000006E0  ; Local APIC TSC Deadline Mode Timer Control Register

; =======================================================================
; 9. MACHINE CHECK ARCHITECTURE (MCA ENGINE - PHYSICAL FAULT DETECTION)
; =======================================================================
%define IA32_MCG_CAP        0x00000179  ; Global Machine Check Capabilities (Read hardware error banks)
%define IA32_MCG_STATUS     0x0000017A  ; Global Machine Check Status (Validates active silicon faults)
%define IA32_MCG_CTL        0x0000017B  ; Global Machine Check Control Interface (Master exception switch)
%define IA32_MC0_CTL        0x00000400  ; Hardware Error Bank 0 Control Register (First silicon unit)
%define IA32_MC0_STATUS     0x00000401  ; Hardware Error Bank 0 Status Frame (Read active hardware faults)

; =======================================================================
; 10. ADVANCED CACHE ALLOCATION TECHNOLOGY (CAT ARCHITECTURE)
; =======================================================================
%define IA32_L3_QOS_MASK_0  0x00000C90  ; L3 Cache Allocation Mask 0 (Isolates Host cache lanes from Guest)
%define IA32_L2_QOS_MASK_0  0x00000D10  ; L2 Cache Allocation Mask 0 (Strict hardware core cache partitioning)
%define IA32_GDT_ALIGN_LOCK 0x000002E0  ; Architectural Alignment Lock MSR (Silicon-level protection against #GP)

; =======================================================================
; 11. MULTI-CORE MANAGEMENT & LOCAL APIC x2APIC REGISTERS
; =======================================================================
%define IA32_X2APIC_ID      0x00000802  ; Read Local APIC ID in x2APIC mode (Identifies current CPU core)
%define IA32_X2APIC_TPR     0x00000808  ; Task Priority Register (Controls interrupt priority thresholds)
%define IA32_X2APIC_PPR     0x0000080A  ; Processor Priority Register (Current execution priority level)
%define IA32_X2APIC_EOI     0x0000080B  ; End of Interrupt Register (Signal hardware that interrupt processing is done)
%define IA32_X2APIC_ICR     0x00000830  ; Interrupt Command Register (Used by Hypervisor to send IPIs between cores)

; =======================================================================
; 12. ARCHITECTURAL PATROL & COUNTER ISOLATION (PLATFORM SECURITY)
; =======================================================================
%define IA32_TSC_RATIO      0x00000064  ; Architectural TSC Ratio Control (Scale factor for matching Guest/Host clocks)
%define IA32_PLATFORM_ID    0x00000017  ; Read platform specific hardware flavor bits (Common testing interface)
%define IA32_BBL_CR_CTL3    0x0000011E  ; L2 Cache Hardware Control Register (Enables hardware scrubbing defenses)

; =======================================================================
; 13. ADDITIONAL CONTROL-FLOW DEFENSES (CET & RETPOLINE REINFORCEMENTS)
; =======================================================================
%define IA32_S_CET_STATUS   0x000006A2  ; Supervisor Shadow Stack Ring 0 Active Status Token tracking
%define IA32_U_CET_STATUS   0x000006A3  ; User Shadow Stack Ring 3 Active Status Token tracking

; =======================================================================
; 14. PROCESSOR UTILIZATION & ACTUAL FREQUENCY COUNTERS
; =======================================================================
%define IA32_MPERF          0x000000E7  ; Maximum Performance Frequency Clock Count
%define IA32_APERF          0x000000E8  ; Actual Performance Frequency Clock Count

; =======================================================================
; 15. ARCHITECTURAL PAT (PAGE ATTRIBUTE TABLE) & MEMORY WRITING
; =======================================================================
%define IA32_CR_PAT         0x00000277  ; Page Attribute Table Layout Register (Controls caching per page)

; =======================================================================
; 16. MISCELLANEOUS HARDWARE STATE & FEATURES ENUMERATION
; =======================================================================
%define IA32_MISC_ENABLE    0x000001A0  ; Miscellaneous Processor Features Enable Frame (XD, SpeedStep)
%define IA32_FEATURE_CONTROL 0x0000003A ; Lock register for enabling VMX/SVM at BIOS level securely

; =======================================================================
; 17. LEGACY COMPATIBILITY & SEGMENT EXPANSIONS (Ring 0 / Ring 3)
; =======================================================================
%define IA32_DS_AREA        0x00000600  ; Debug Store Area (Allocates a physical buffer boundary for BTS and PEBS)
%define IA32_EBC_FREQUENCY  0x0000002C  ; Processor Front Side Bus (FSB) / Core Frequency Scaling Status register

; =======================================================================
; 18. PROCESSOR INVENTORY & SERIALIZATION CONTROL
; =======================================================================
%define IA32_PPIN_CTL       0x0000004E  ; Protected Processor Inventory Number Control (Lock/Enable)
%define IA32_PPIN           0x0000004F  ; Read-only 64-bit unique physical silicon identifier serial number

; =======================================================================
; 19. PREFETCH CONTROL & AMBIENT PERFORMANCE TUNING
; =======================================================================
%define IA32_MISC_PREFETCH_CTL 0x000001A4 ; Hardware Prefetcher Control Register (Disable/Enable L1/L2 prefetchers)

; =======================================================================
; 20: LEGACY BARE-METAL OUTPUT (VGA & SERIAL COM1) FOR X86
; =======================================================================
%define X86_COM1_PORT       0x3F8       ; Serial Port COM1 Address (Used with OUT instruction)
%define VGA_TEXT_MODE_BASE  0x000B8000  ; Physical memory address of the screen (Write ASCII here to show text)

; =======================================================================
; 21: ARCHITECTURAL CPU FLAGS & EFER BITS FOR X86_64
; =======================================================================
%define EFLAGS_IF_BIT       9           ; Interrupt Flag (1 = Physical interrupts enabled)
%define EFLAGS_VM_BIT       17          ; Virtual 8086 Mode Flag (Used to detect legacy guests)

%define EFER_LME_BIT        8           ; Long Mode Enable (Turn on 64-bit architecture support)
%define EFER_LMA_BIT        10          ; Long Mode Active (Read-only: status that 64-bit is running)
%define EFER_SVME_BIT       12          ; SVM Enable (Crucial flag to unlock AMD Virtualization)


; =======================================================================
; AMD64 SPECIFIC MSR DEFINITIONS (AUTHENTICAMD) - FULL COMPREHENSIVE BANK
; =======================================================================

; =======================================================================
; 1. CORE AMD SVM VIRTUALIZATION MASTER CONTROLS (FIXED & VERIFIED)
; =======================================================================
%define VM_CR_MSR           0xC0010114  ; SVM Hardware Virtualization Configuration & Lock Register (Fixed address)
%define VM_HSAVE_PA_MSR     0xC0010117  ; Host Save Area Physical Address (AMD SVM core requirement - Fixed address)
%define MSR_AMD_SMM_ADDR    0xC0010112  ; SMM TSEG Base Address Register (Physical SMM protection)
%define MSR_VM_IGNNE        0xC0010115  ; SVM Ignore Numeric Error Mitigation Register (Legacy virtualization lock)
%define MSR_HW_CR           0xC0010015  ; Hardware Configuration Register (TSC frequency lock parameters)

; =======================================================================
; 2. ADVANCED HARDWARE ENCRYPTION & ATTRIBUTES (AMD SEV / SEV-SNP)
; =======================================================================
%define MSR_SEV_STATUS      0xC0010131  ; AMD Secure Encrypted Virtualization Active Features Status
%define MSR_VCPU_ID         0xC001013A  ; AMD SEV-SNP Guest Virtual CPU Attestation Identifier
%define MSR_SEV_FEATURES    0xC001013E  ; AMD SEV Supported Security Features Extension Bitmap

; =======================================================================
; 3. AMD INTERNALS, DECODE CONFIGURATION & SILICON SPECULATION DEFENSE
; =======================================================================
%define MSR_DE_CFG          0xC0011029  ; Decode Configuration Register (Used to patch execution bugs like Zenbleed)
%define MSR_LS_CFG          0xC0011020  ; Load-Store Configuration Frame (Controls speculative execution serialization)
%define MSR_IC_CFG          0xC0011021  ; Instruction Cache Configuration (Silicon execution engine hardening)

; =======================================================================
; 4. AMD ADVANCED SYSTEM ARCHITECTURE & EXCEPTION VECTOR EXTENSIONS
; =======================================================================
%define MSR_AMD_PATCH_LEVEL 0x0000008B  ; Current Microcode Patch Level Revision (Read-Only validation)
%define MSR_NB_CFG          0xC001001F  ; Northbridge Configuration Interface (Advanced memory profiling)
%define MSR_EXT_FEATURES    0xC0010058  ; Extended Exception Vector Configuration and Silicon Attributes

; =======================================================================
; 5. MSR PERMISSIONS MAPS (MSRP ARCHITECTURE FOR HARDWARE BLOCKING)
; =======================================================================
%define MSRP_BASE_ADDRESS   0x00010000  ; Recommended 8KB physical buffer boundary for MSRP
%define MSRP_OFFSET_DEVICE  0x00002004  ; VMCB Control area byte pointer anchoring the MSRP layout

; =======================================================================
; 6. I/O VIRTUALIZATION TECHNOLOGY & HARDWARE DMA PROTECTION (IOMMU)
; =======================================================================
%define MSR_IOMMU_BASE      0xC0010074  ; IOMMU Base Address Register (Controls hardware DMA mapping safety)
%define MSR_IOMMU_CONTROL   0xC0010075  ; IOMMU Execution Control Frame (Locks DMA from external chips)

; =======================================================================
; 7. AMD HARDWARE P-STATE & FREQUENCY CONTROL
; =======================================================================
%define MSR_AMD_PSTATE_LIMIT 0xC0010061 ; P-State Current Limit (Reads the maximum allowed hardware performance state)
%define MSR_AMD_PSTATE_CTL   0xC0010062 ; P-State Control Register (Allows Hypervisor to force a core into lower power)
%define MSR_AMD_PSTATE_STAT  0xC0010063 ; P-State Status Register (Reads current active hardware multiplier)
%define MSR_AMD_TSC_RATIO    0xC0000104 ; AMD Specific TSC Ratio (Hypervisor TSC scaling control for legacy processors)

; =======================================================================
; 8. AMD OPERATING SYSTEM VISIBLE WORKAROUNDS (OSVW ENGINE)
; =======================================================================
%define MSR_AMD_OSVW_ID_LEN  0xC0010140 ; OSVW ID Length Register (Number of hardware errata tracked by CPU)
%define MSR_AMD_OSVW_STATUS  0xC0010141 ; OSVW Status Register (Bitmap showing which bugs require software fixes)

; =======================================================================
; 9. AMD HARDWARE SPECULATION DEFENSES & BRANCH HARDENING
; =======================================================================
%define MSR_AMD_BP_CFG       0xC001102E ; Branch Predictor Configuration (Used to enable BpSpecReduce for SRSO mitigation)
%define MSR_AMD_BU_CFG2      0xC001102B ; Bus Unit Configuration 2 (Contains custom serialization flags for speculation)

; =======================================================================
; 10. AMD INSTRUCTION-BASED SAMPLING (IBS CONTROLS - FETCH)
; =======================================================================
%define MSR_AMD_IBSFETCHCTL  0xC0011030 ; IBS Fetch Control Register (Manages tag-on-fetch tracking for instructions)
%define MSR_AMD_IBSFETCHLINAD 0xC0011031 ; IBS Fetch Linear Address Register (Reads the RIP that triggered the fetch event)
%define MSR_AMD_IBSFETCHPHYSAD 0xC0011032 ; IBS Fetch Physical Address Register (The actual RAM location used)
%define MSR_AMD_IBSOPCTL     0xC0011033 ; IBS Execution Control Register (Tracks execution and macro-op routing)

; =======================================================================
; 11. AMD SMM CONTROLS & HYPERVISOR SILICON LOCKDOWN
; =======================================================================
%define MSR_AMD_SMM_CTL     0xC0010116  ; SMM Control Register (Locks or enables SMM entry and virtualization intercepts)
%define MSR_AMD_SMBASE      0xC0010111  ; SMM Base Address Register (Defines the physical relocation boundary of SMRAM)

; =======================================================================
; 12. AMD ADVANCED VIRTUAL INTERRUPT CONTROLLER (AVIC REGISTERS)
; =======================================================================
%define MSR_AMD_AVIC_DOORBELL 0xC001011B ; AVIC Doorbell MSR (Used by the Host core to trigger an immediate guest virtual interrupt)

; =======================================================================
; 13. AMD PROCESSOR CONFIGURATION & BRANDING MAPS
; =======================================================================
%define MSR_AMD_NAME_STRING_0 0xC0010030 ; Processor Name String Register 0 (Holds first 8 ASCII characters of CPU name)
%define MSR_AMD_NAME_STRING_1 0xC0010031 ; Processor Name String Register 1
%define MSR_AMD_NAME_STRING_2 0xC0010032 ; Processor Name String Register 2
%define MSR_AMD_NAME_STRING_3 0xC0010033 ; Processor Name String Register 3
%define MSR_AMD_NAME_STRING_4 0xC0010034 ; Processor Name String Register 4
%define MSR_AMD_NAME_STRING_5 0xC0010035 ; Processor Name String Register 5

; =======================================================================
; 14. AMD CCX TOPOLOGY & CACHE COHERENCY MATRIX
; =======================================================================
%define MSR_AMD_CCX_CORE_ID 0xC001100C  ; Read-only physical Core ID/Node ID for NUMA topology (Fixed address)
%define MSR_AMD_L3_CONFIG   0xC0011022  ; AMD Specific L3 Cache Partitioning and Interleave Control

; =======================================================================
; 15. CORE POWER MONITORING & ENERGY LIMITS
; =======================================================================
%define MSR_RAPL_POWER_UNIT 0xC0010299  ; Running Average Power Limit (RAPL) Power Unit Frame
%define MSR_PKG_ENERGY_STATUS 0xC001029B ; Read-only actual silicon package cumulative energy usage

; =======================================================================
; 16. ADVANCED CPPC PERFORMANCE HARDWARE TUNING (NEW EXTENSION)
; =======================================================================
%define MSR_AMD_CPPC_CAP1   0xC00102B0  ; CPPC Target Capability Register (Highest/efficient silicon frequencies)
%define MSR_AMD_CPPC_ENABLE 0xC00102B1  ; CPPC Hardware Core Optimization Enable Switch
%define MSR_AMD_CPPC_REQ    0xC00102B3  ; CPPC Request Register (Force dynamic vCPU clock limits)

; =======================================================================
; 17. INSTRUCTION-BASED SAMPLING EXECUTION TRACKING (NEW EXTENSION)
; =======================================================================
%define MSR_AMD64_IBSOPRIP  0xC0011034  ; Exact RIP causing pipeline stalls during execution sampling
%define MSR_AMD64_IBSOPDATA 0xC0011035  ; IBS Op Data (Cache misses, memory attributes and hardware faults)
%define MSR_AMD64_IBSOPDATA2 0xC0011036 ; IBS Op Data 2 Register (Exact execution timing in hardware cycles)

; =======================================================================
; 18. RUNTIME MICROCODE INJECTION ENGINE (NEW EXTENSION)
; =======================================================================
%define MSR_AMD_PATCH_LOADER 0xC0010020 ; Microcode Patch Loader Register (Inject updates straight to silicon)

; =======================================================================
; AMD64 SPECIFIC MSR DEFINITIONS (PART 2 - THE ULTIMATE EXTENSION)
; =======================================================================

; =======================================================================
; 19. AMD x2AVIC VIRTUAL x2APIC SYSTEM MONITORING (CRITICAL FOR GUEST INTERRUPTS)
; =======================================================================
%define MSR_AMD_X2APIC_ID       0x00000802  ; Virtual x2APIC ID MSR (Intercepted by x2AVIC to identify guest vCPU)
%define MSR_AMD_X2APIC_TPR      0x00000808  ; Task Priority Register (Controls guest interrupt filtering)
%define MSR_AMD_X2APIC_PPR      0x0000080A  ; Processor Priority Register (Current execution priority level)
%define MSR_AMD_X2APIC_EOI      0x0000080B  ; End of Interrupt Register (Signaled by Guest without VM-Exit)
%define MSR_AMD_X2APIC_LDR      0x0000080D  ; Logical Destination Register for virtual interrupt routing
%define MSR_AMD_X2APIC_Spurious 0x0000080F  ; Spurious Interrupt Vector Register
%define MSR_AMD_X2APIC_ISR0     0x00000810  ; In-Service Register Frame 0 (Tracks active virtual interrupts)
%define MSR_AMD_X2APIC_ICR      0x00000830  ; Interrupt Command Register (x2AVIC emulates inter-processor interrupts)

; =======================================================================
; 20. AMD SPECIFIC CACHE CONTROLS & MEMORY CONFIGURATION
; =======================================================================
%define MSR_AMD_SYS_CFG         0xC0000010  ; System Configuration (Contains MtrrFixDramEn to lock fixed MTRRs)
%define MSR_AMD_TOP_MEM         0xC001001A  ; Top of Memory 1 (Defines boundary between normal RAM and MMIO space)
%define MSR_AMD_TOP_MEM2        0xC001001D  ; Top of Memory 2 (Defines memory space upper boundaries above 4GB)

; =======================================================================
; 21. AMD FIXED-RANGE MTRR MEMORY ACCESS TYPE MAPS
; =======================================================================
%define MSR_AMD_MTRRfix64k_00000 0x00000250 ; AMD Fixed MTRR for lowest 64KB block (Controls physical DRAM caching)
%define MSR_AMD_MTRRfix16k_80000 0x00000258 ; Fixed MTRR mapping for 80000h–9FFFFh physical address space
%define MSR_AMD_MTRRfix16k_A0000 0x00000259 ; Fixed MTRR mapping for A0000h–BFFFFh physical address space
%define MSR_AMD_MTRRfix4k_C0000  0x00000268 ; Fixed MTRR mapping for C0000h–C7FFFh video BIOS space

; =======================================================================
; AMD64 SPECIFIC MSR DEFINITIONS (PART 3 - THE PMU & PERFORMANCE SHIELD)
; =======================================================================

; =======================================================================
; 22. AMD CORE PERFORMANCE COUNTER EVENT SELECTORS (PERF_CTL)
; =======================================================================
%define MSR_AMD_PERF_CTL0       0xC0010000  ; Performance Event Select 0 (Controls what hardware event to monitor)
%define MSR_AMD_PERF_CTL1       0xC0010001  ; Performance Event Select 1
%define MSR_AMD_PERF_CTL2       0xC0010002  ; Performance Event Select 2
%define MSR_AMD_PERF_CTL3       0xC0010003  ; Performance Event Select 3
%define MSR_AMD_PERF_CTL4       0xC0010200  ; Performance Event Select 4 (Extended counter for modern Zen cores)
%define MSR_AMD_PERF_CTL5       0xC0010202  ; Performance Event Select 5

; =======================================================================
; 23. AMD CORE PERFORMANCE COUNTER DATA REGISTERS (PERF_CTR)
; =======================================================================
%define MSR_AMD_PERF_CTR0       0xC0010004  ; Performance Counter Data 0 (Holds the actual hardware event count)
%define MSR_AMD_PERF_CTR1       0xC0010005  ; Performance Counter Data 1
%define MSR_AMD_PERF_CTR2       0xC0010006  ; Performance Counter Data 2
%define MSR_AMD_PERF_CTR3       0xC0010007  ; Performance Counter Data 3
%define MSR_AMD_PERF_CTR4       0xC0010201  ; Performance Counter Data 4 (Extended data frame)
%define MSR_AMD_PERF_CTR5       0xC0010203  ; Performance Counter Data 5

; =======================================================================
; 24. AMD SPECIFIC VIRTUALIZATION SECURITY HARDENING (LBR & DEEP TRAILING)
; =======================================================================
%define MSR_AMD_LBR_SELECT      0xC00101C0  ; AMD Last Branch Record Select (Filter which branches the CPU records)
%define MSR_AMD_LBR_FROM_IP     0xC00101C1  ; LBR Stack From IP (Where the execution jump came from)
%define MSR_AMD_LBR_TO_IP       0xC00101C2  ; LBR Stack To IP (Where the execution jump landed)

; =======================================================================
; 25. AMD HARDWARE PASSWORD-PROTECTED DEBUG MSRs 
; (Requires EDI = 0x9C5A203A before execution to avoid #GP)
; =======================================================================
%define MSR_AMD_EXT_DEBUG_BASE  0xC001100A  ; Hidden Debug Controller Configuration Register
%define MSR_AMD_EXT_DEBUG_DATA  0xC001100B  ; Debug Output Buffer Register (Reads physical silicon states)

; =======================================================================
; 26. UNDOCUMENTED AMD MSR BREAKPOINT TRAPS
; =======================================================================
%define MSR_AMD_BREAKPOINT      0xC001100E  ; MSR Breakpoint Target Address (Triggers hardware intercept)
%define MSR_AMD_BREAKPOINT_MASK 0xC001100F  ; MSR Breakpoint Filter Mask (Defines target bits range)

; =======================================================================
; 27. UNDOCUMENTED BUS ARCHITECTURE & BRANCH TRACING (BHTrace Engine)
; =======================================================================
%define MSR_AMD_BHTRACE_CTL     0xC0011010  ; Bus Hardware Trace Master Control Switch
%define MSR_AMD_BHTRACE_DATA    0xC0011011  ; Bus Hardware Trace User Data Collect Frame

; =======================================================================
; 28. UNDOCUMENTED SILICON ISOLATION & PREFETCH LOCKS
; =======================================================================
%define MSR_AMD_DC_CFG_SECRET   0xC0011022  ; Undocumented Data Cache configuration for disabling prefetchers

; =======================================================================
; AMD64 SPECIFIC MSR DEFINITIONS (THE FORBIDDEN DEEP-SILICON EXTENSION)
; =======================================================================

; =======================================================================
; 29. AMD PERFORMANCE BOOST & THERMAL RATIO LOCKS (INTERNAL TUNING)
; =======================================================================
%define MSR_AMD_CORED_CFG       0xC001102C  ; Core Performance Configuration (Hidden switch used by AMD Ryzen Master to bypass boost limits)
%define MSR_AMD_THM_CR_CYC      0xC0010073  ; Thermal Hardware Cycle Modulation (Directly controls physical throttling)

; =======================================================================
; 30. AMD EXPERIMENTAL SPECULATION HARDENING (Zen 4 / Zen 5 Shielder)
; =======================================================================
%define MSR_AMD_PPIN_CTL_SECRET 0xC001004E  ; Hidden Protected Processor Inventory Number Lock Control
%define MSR_AMD_SPECTRE_V4_CTL  0xC0011024  ; Custom speculative store bypass disable (Alternative hardware-level mitigation address)

; =======================================================================
; 31. AMD HARDWARE ERROR INJECTION & SILICON CORRUPTION INTRUSION
; =======================================================================
%define MSR_AMD_ERR_INJECT      0xC001011E  ; Machine Check Architecture Error Injection Trigger (Forces simulated silicon faults)
%define MSR_AMD_ERR_STATUS_MASK 0xC001011F  ; MCA Hardware Bank Intercept Filter Mask

; =======================================================================
; 32. AMD EMBEDDED CO-PROCESSOR SECURITY SHIELDS (PSP GATEWAY)
; =======================================================================
%define MSR_AMD_PSP_COMMAND     0xC00110A0  ; Platform Security Processor Host Command Pipeline Interface
%define MSR_AMD_PSP_STATUS      0xC00110A1  ; Platform Security Processor Hardware Fuses and Active Status Frame

; =======================================================================
; AMD64 SPECIFIC MSR DEFINITIONS (THE FINAL SILICON BREAKPOINT LAYER)
; =======================================================================

; =======================================================================
; 33. AMD EXTENDED MACHINE CHECK ARCHITECTURE (MCA EXTRAS)
; =======================================================================
%define MSR_AMD_MCA_CFG         0xC0010044  ; MCA Configuration Register (Determines how silicon errors are reported to Host)
%define MSR_AMD_MCA_EXT_CTL0    0xC0010050  ; Extended MCA Control for Core Bank 0 (Advanced hardware logging)
%define MSR_AMD_MCA_EXT_STAT0   0xC0010051  ; Extended MCA Status Frame 0 (Reads physical silicon error telemetry)

; =======================================================================
; 34. AMD ARCHITECTURAL THREAD TOPOLOGY & THREAD PREFERENCE
; =======================================================================
%define MSR_AMD_TH_PR_CTL       0xC0011028  ; Thread Preference Control (Allows the Hypervisor to prioritize specific vCPUs)
%define MSR_AMD_ASYM_CORE_MAP   0xC001103A  ; Asymmetric Core Mapping (Identifies high-performance vs. efficient cores in modern Zen layouts)

; =======================================================================
; 35. AMD SILICON DEBUGGER EMULATION INTERCEPT
; =======================================================================
%define MSR_AMD_HDT_CTRL        0xC001100D  ; Hardware Debug Tool Intercept (Captures physical debugger connection events)

; =======================================================================
; AMD64 SPECIFIC MSR DEFINITIONS (THE FORBIDDEN FABRIC LAYER - UNDOCUMENTED)
; =======================================================================

; =======================================================================
; 36. AMD INFINITY FABRIC DATA ROUTING CONTROLS (UNDOCUMENTED)
; =======================================================================
%define MSR_AMD_FABRIC_CFG      0xC0011000  ; Infinity Fabric Configuration (Controls inter-core communication priorities)
%define MSR_AMD_FABRIC_SNOOP    0xC0011003  ; Hidden Fabric Snoop Control Register (Intercepts cache invalidation signals)

; =======================================================================
; 37. AMD ARCHITECTURAL DATA ALIGNMENT FLUSH SWITCH (UNDOCUMENTED)
; =======================================================================
%define MSR_AMD_ALIGN_FORCE     0xC0011018  ; Force Alignment Flush Register (Secret bit here forces memory access serialization)

; =======================================================================
; 38. AMD ZEN MICROARCHITECTURAL LOCK REGISTERS (UNDOCUMENTED FEATURE LOCKS)
; =======================================================================
%define MSR_AMD_FEATURE_LOCK0   0xC001102A  ; Hardware Level Feature Disable Lock (Used by microcode to patch silicon at runtime)
%define MSR_AMD_FPU_CFG_SECRET  0xC001102F  ; Floating-Point Unit Hidden Configuration (Controls speculative AVX-512 execution blocks)

; =======================================================================
; 39. AMD MICROARCHITECTURAL EXECUTING CHICKEN BITS (UNDOCUMENTED EX_CFG)
; =======================================================================
%define MSR_AMD_EX_CFG          0xC0011021  ; Execution Unit Configuration (Bits here can disable specific hardware optimization pipelines inside ALU)
%define MSR_AMD_EX_CFG2         0xC001102D  ; Extended Execution Controls (Used by AMD hot-loadable microcode patches to mitigate data leaks)

; =======================================================================
; 40. AMD STACK-POINTER SPECULATION DEFENSE (THE STACKWARP SHIELD - CVE-2025-29943)
; =======================================================================
%define MSR_AMD_LS_CFG2         0xC0011023  ; Load-Store Configuration 2 (Contains the secret bit flipped by July 2025 patches to prevent StackWarp VM integrity breaks)

; =======================================================================
; 41. AMD FLOATING POINT & AVX VECTOR BALANCING (UNDOCUMENTED FP_CFG)
; =======================================================================
%define MSR_AMD_FP_CFG          0xC0011028  ; Floating Point Unit Configuration (Alters execution timing of heavy vector instructions to avoid power surges)



;========================================================================
; Part 3 - only MSRS for Intel:
;========================================================================

; =======================================================================
; 1. INTEL TRANSACTIONAL EXECUTION & LEGACY ENTRY INTERFACES
; =======================================================================
%define IA32_TSX_CTRL       0x00000122  ; Intel TSX Transactional Execution Control (TAA mitigation)
%define IA32_SYSENTER_CS    0x00000174  ; Target Code Segment Selector for SYSENTER instruction
%define IA32_SYSENTER_ESP   0x00000175  ; Isolated Target Stack Pointer (ESP) for SYSENTER routine
%define IA32_SYSENTER_EIP   0x00000176  ; Target Execution Instruction Pointer (EIP) entry vector

; =======================================================================
; 2. ADVANCED HARDWARE TRACING & PROCESSOR TRACE FRAMEWORK (INTEL PT)
; =======================================================================
%define IA32_RTIT_CTL       0x00000570  ; Real-Time Instruction Trace Control (Silicon execution logging)
%define IA32_RTIT_STATUS    0x00000571  ; Real-Time Instruction Trace Status (Monitor tracer execution state)
%define IA32_RTIT_OUTPUT_BASE 0x00000560 ; Direct hardware tracing target physical memory lane allocation

; =======================================================================
; 3. INTEL VMX MASTER CONTROL REGISTERS (Essential Architectural Capabilities)
; =======================================================================
%define IA32_VMX_BASIC               0x00000480 ; VMX Basic Info Capabilities
%define IA32_VMX_PINBASED_CTLS       0x00000481 ; VMX Pin-based Execution Control Caps
%define IA32_VMX_PROCBASED_CTLS      0x00000482 ; VMX Primary Processor Controls
%define IA32_VMX_EXIT_CTLS           0x00000483 ; VMX VM-Exit Controls Capabilities
%define IA32_VMX_ENTRY_CTLS          0x00000484 ; VMX VM-Entry Controls Capabilities
%define IA32_VMX_MISC                0x00000485 ; VMX Miscellaneous Capabilities
%define IA32_VMX_CR0_FIXED0          0x00000486 ; VMX CR0 Fixed Bits Requirements
%define IA32_VMX_CR0_FIXED1          0x00000487 
%define IA32_VMX_CR4_FIXED0          0x00000488 ; VMX CR4 Fixed Bits Requirements
%define IA32_VMX_CR4_FIXED1          0x00000489
%define IA32_VMX_PROCBASED_CTLS2     0x0000048B ; VMX Secondary Processor Controls (Required for EPT)
%define IA32_VMX_EPT_VPID_CAP        0x0000048C ; VMX Extended Page Table Capabilities

; =======================================================================
; 4. INTEL VMX BASIC & PIN-BASED CONTROLS (CAPABILITIES)
; =======================================================================
%define IA32_VMX_BASIC               0x00000480 ; VMX Basic Info Capabilities (Contains VMCS Revision ID)
%define IA32_VMX_PINBASED_CTLS       0x00000481 ; VMX Pin-based Execution Control Caps (External interrupts, NMI)

; =======================================================================
; 5. INTEL VMX PROCESSOR-BASED EXECUTION CONTROLS
; =======================================================================
%define IA32_VMX_PROCBASED_CTLS      0x00000482 ; VMX Primary Processor Controls (CR3 load/store intercepts, RDTSC intercept)
%define IA32_VMX_PROCBASED_CTLS2     0x0000048B ; VMX Secondary Processor Controls (Essential for enabling EPT and VPID)
%define IA32_VMX_PROCBASED_CTLS3     0x00000492 ; VMX Tertiary Processor Controls (Recent CPUs - used for HLAT and advanced features)

; =======================================================================
; 6. INTEL VMX TRANSITION CONTROLS (ENTRY & EXIT)
; =======================================================================
%define IA32_VMX_EXIT_CTLS           0x00000483 ; VMX VM-Exit Controls Capabilities (Host address space width, MSR save/load)
%define IA32_VMX_ENTRY_CTLS          0x00000484 ; VMX VM-Entry Controls Capabilities (Guest 64-bit mode, MSR load, event injection)

; =======================================================================
; 7. INTEL VMX REGISTER RESTRICTIONS & EXTENSIONS
; =======================================================================
%define IA32_VMX_CR0_FIXED0          0x00000486 ; VMX CR0 Fixed Bits Requirements (Bits that MUST be set to 0 in VMX)
%define IA32_VMX_CR0_FIXED1          0x00000487 ; VMX CR0 Fixed Bits Requirements (Bits that MUST be set to 1 in VMX)
%define IA32_VMX_CR4_FIXED0          0x00000488 ; VMX CR4 Fixed Bits Requirements
%define IA32_VMX_CR4_FIXED1          0x00000489
%define IA32_VMX_MISC                0x00000485 ; VMX Miscellaneous Capabilities (Activity states, MSC guest size)

; =======================================================================
; 8. INTEL EPT & VPID ADVANCED ARCHITECTURAL CAPABILITIES
; =======================================================================
%define IA32_VMX_EPT_VPID_CAP        0x0000048C ; VMX Extended Page Table and VPID Capabilities (Supported page sizes: 2MB/1GB)

; =======================================================================
; 9. INTEL SOFTWARE GUARD EXTENSIONS (SGX MASTER CONTROLS)
; =======================================================================
%define IA32_SGX_SVN_STATUS          0x00000500 ; SGX Security Version Number Status register
%define IA32_SGX_ATTRIBUTES          0x00000501 ; SGX Architectural Enclave Attributes Configuration Mask

; =======================================================================
; 10. INTEL TRUSTED EXECUTION TECHNOLOGY (TXT & SMX SECURE BOOT)
; =======================================================================
%define IA32_TXT_PUBLIC_BASE         0xFEB00000 ; Memory-mapped Base for TXT Public Config Registers (Architectural target)

; =======================================================================
; 11. INTEL VMX VIRTUAL INTERRUPT & APIC VIRTUALIZATION CAPS
; =======================================================================
%define IA32_VMX_VMFUNC              0x00000491 ; VMX Function Capabilities (Allows Guest to switch EPT views without VM-Exit)

; =======================================================================
; 12. INTEL RESOURCE DIRECTOR TECHNOLOGY (RDT QUALITY OF SERVICE)
; =======================================================================
%define IA32_PQR_ASSOC               0x00000C8F ; Allocates Class of Service (CLOS) ID to the active vCPU core
%define IA32_L3_MON_QOS_ID           0x00000C8D ; L3 Cache Monitoring Resource Quality of Service Identifier
%define IA32_QM_EVTSEL               0x00000C8D ; QoS Monitoring Event Select Register (Tracks L3 occupancy/cache misses)
%define IA32_QM_CTR                  0x00000C8E ; QoS Monitoring Event Counter Data Register

; =======================================================================
; 13. INTEL THREAD DIRECTOR & HARDWARE FEEDBACK INTERFACE (HFI)
; =======================================================================
%define IA32_HW_FEEDBACK_PTR         0x000017D0 ; Physical base pointer boundary for the HFI structure table in RAM
%define IA32_HW_FEEDBACK_CONFIG      0x000017D1 ; Master hardware switch to enable/disable Intel Thread Director feedback

; =======================================================================
; 14. INTEL SMBASE RELOCATION & CORE SILICON LOCKDOWN
; =======================================================================
%define IA32_SMBASE                  0x0000009E ; Intel Specific SMM Base relocation mapping address (Protected Ring -2 target)
%define IA32_VMX_MISC_MSR            0x00000485 ; VMX Miscellaneous Architectural Status flags

; =======================================================================
; 15. RECENT HARDWARE FRED INTERFACES (Flexible Return and Event Delivery)
; =======================================================================
%define IA32_FRED_RSP0      0x000001CC  ; Flexible Return level 0 Stack Pointer Target Vector
%define IA32_FRED_RSP1      0x000001CD  ; Flexible Return level 1 Stack Pointer Target Vector
%define IA32_FRED_CONFIG    0x000001D4  ; FRED Architectural Master Configuration Setup Frame

; =======================================================================
; 16. ADVANCED HARDWARE SPECULATION DEFENSES & HARDENING
; =======================================================================
%define IA32_UARCH_MISC_CTL 0x000001B0  ; Microarchitectural Miscellaneous Control (Locks DOITM to defeat side-channel leaks)
%define IA32_SGX_LEPUBKEYHASH0 0x0000008C ; SGX Launch Enclave Public Key Hash Frame 0 (Common on modern microarchitectures)
%define IA32_SGX_LEPUBKEYHASH1 0x0000008D ; SGX Launch Enclave Public Key Hash Frame 1
%define IA32_SGX_LEPUBKEYHASH2 0x0000008E ; SGX Launch Enclave Public Key Hash Frame 2
%define IA32_SGX_LEPUBKEYHASH3 0x0000008F ; SGX Launch Enclave Public Key Hash Frame 3

; =======================================================================
; 17. CORE POWER MONITORING & ENERGY LIMITS
; =======================================================================
%define MSR_RAPL_POWER_UNIT 0x00000606  ; Running Average Power Limit (RAPL) Power Unit Frame
%define MSR_PKG_ENERGY_STATUS 0x00000611 ; Read-only actual silicon package cumulative energy usage



;there is for RISC-V and ARM:

; =======================================================================
; ARM64- PART 3: GICv3/v4 VIRTUAL INTERRUPT INTERFACE
; =======================================================================

; -----------------------------------------------------------------------
; ICH_HCR_EL2 - Interrupt Controller Hypervisor Control Register
; The master switch for the virtual GIC interface at EL2. Enables or disables
; the virtual CPU interface for the guest and controls signaling of virtual
; interrupts and maintenance interrupts back to the hypervisor.
; -----------------------------------------------------------------------
%define ICH_HCR_EL2         ICH_HCR_EL2

; -----------------------------------------------------------------------
; ICH_VTR_EL2 - Interrupt Controller Virtual Type Register
; Read-only capabilities register. Tells your hypervisor how many List
; Registers (LRs) are physically implemented in the silicon, the number of
; virtual priority bits, and virtual identifier bits supported.
; -----------------------------------------------------------------------
%define ICH_VTR_EL2         ICH_VTR_EL2

; -----------------------------------------------------------------------
; ICH_VMCR_EL2 - Interrupt Controller Virtual Machine Control Register
; Controls the virtual CPU interface for the current guest VM. It manages
; virtual interrupt masking, group enables, and priority thresholds, acting
; as the EL2 proxy for the guests ICC_MCT_EL1/ICC_PMR_EL1 registers.
; -----------------------------------------------------------------------
%define ICH_VMCR_EL2        ICH_VMCR_EL2

; -----------------------------------------------------------------------
; ICH_MISR_EL2 - Interrupt Controller Maintenance Interrupt Status Register
; Indicates which hypervisor maintenance interrupt events are active. Useful
; for knowing when a guest has acknowledged an interrupt or when the List
; Registers are running empty and need refilling by your scheduler.
; -----------------------------------------------------------------------
%define ICH_MISR_EL2        ICH_MISR_EL2

; -----------------------------------------------------------------------
; ICH_LR0_EL2 to ICH_LR15_EL2 - Interrupt Controller List Registers
; Array of hardware registers used by the hypervisor to inject specific
; virtual interrupts into the guest. Each register holds the interrupt ID,
; priority, state (pending, active, or both), and routing information.
; -----------------------------------------------------------------------
%define ICH_LR0_EL2         ICH_LR0_EL2
%define ICH_LR1_EL2         ICH_LR1_EL2
%define ICH_LR2_EL2         ICH_LR2_EL2
%define ICH_LR3_EL2         ICH_LR3_EL2
%define ICH_LR4_EL2         ICH_LR4_EL2
%define ICH_LR5_EL2         ICH_LR5_EL2
%define ICH_LR6_EL2         ICH_LR6_EL2
%define ICH_LR7_EL2         ICH_LR7_EL2

; Note: Implementations can have up to 16 List Registers depending on ICH_VTR_EL2

; =======================================================================
; ARM64 - PART 4: COPROCESSOR TRAPS & HARDENING
; =======================================================================

; -----------------------------------------------------------------------
; CPACR_EL2 - Architectural Coprocessor Access Control Register (EL2)
; Controls traps to EL2 for guest accesses to Floating Point (FP), Advanced 
; SIMD (NEON), and SVE vector execution units. Vital for lazy context 
; switching of heavy vector registers to maximize hypervisor speed.
; -----------------------------------------------------------------------
%define CPACR_EL2           CPACR_EL2

; -----------------------------------------------------------------------
; MDCR_EL2 - Monitor Debug Configuration Register (EL2)
; Manages hardware debug infrastructure capabilities. Allows the hypervisor
; to intercept or isolate guest access to hardware breakpoints, watchpoints, 
; and performance profiling units (PMU) to prevent host information leaks.
; -----------------------------------------------------------------------
%define MDCR_EL2            MDCR_EL2

; -----------------------------------------------------------------------
; APIAKeyLo_EL1 / APIAKeyHi_EL1 - Pointer Authentication Key Registers
; ARM64 Pointer Authentication (PAC) execution keys context. The hypervisor 
; must back up and restore these hardware keys during the context switch 
; sequence to prevent a malicious guest from forging code pointers.
; -----------------------------------------------------------------------
%define APIAKeyLo_EL1       APIAKeyLo_EL1
%define APIAKeyHi_EL1       APIAKeyHi_EL1

; -----------------------------------------------------------------------
; MACR_EL2 - Memory Attribute Configuration Register (EL2)
; Maps architectural physical memory profiling lanes. Configures access permissions
; and cacheability controls for hardware memory tags within the hypervisor domain.
; -----------------------------------------------------------------------
%define MACR_EL2            MACR_EL2

; =======================================================================
; ARM64 - PART 5: VIRTUAL TIMERS & ACCURATE TIMING
; =======================================================================

; -----------------------------------------------------------------------
; CNTVOFF_EL2 - Counter-timer Virtual Offset Register
; Holds the 64-bit offset value applied to the physical counter to generate 
; the virtual counter value seen by the guest VM (CNTVCT_EL0). Crucial for 
; hiding hypervisor execution latency and preventing anti-virtualization.
; -----------------------------------------------------------------------
%define CNTVOFF_EL2         CNTVOFF_EL2

; -----------------------------------------------------------------------
; CNTHCTL_EL2 - Counter-timer Hypervisor Control Register
; Controls guest access to the physical and virtual timers. Allows the 
; hypervisor to trap guest attempts to read counter values or configure 
; the timer hardware, forcing a transition to EL2 when necessary.
; -----------------------------------------------------------------------
%define CNTHCTL_EL2         CNTHCTL_EL2

; -----------------------------------------------------------------------
; CNTHP_CTL_EL2 - Counter-timer Hypervisor Physical Timer Control Register
; Controls the hypervisor's own physical timer. Used by your scheduler to 
; set a hardware deadline, forcing the CPU to return control to the host 
; when a guest VM's time slice expires.
; -----------------------------------------------------------------------
%define CNTHP_CTL_EL2       CNTHP_CTL_EL2

; -----------------------------------------------------------------------
; CNTHP_CVAL_EL2 - Counter-timer Hypervisor Physical Timer Compare Value
; Holds the absolute 64-bit threshold value for the hypervisors physical 
; timer. An interrupt is triggered when the physical counter reaches this value.
; -----------------------------------------------------------------------
%define CNTHP_CVAL_EL2      CNTHP_CVAL_EL2

; -----------------------------------------------------------------------
; CNTHP_TVAL_EL2 - Counter-timer Hypervisor Physical Timer Timer Value
; Holds the relative countdown value for the hypervisors physical timer. 
; Writes an execution delta directly into the silicon to schedule the next interrupt.
; -----------------------------------------------------------------------
%define CNTHP_TVAL_EL2      CNTHP_TVAL_EL2

; =======================================================================
; ARM64 - PART 6: FEATURE & IDENTITY REGISTERS
; =======================================================================

; -----------------------------------------------------------------------
; MIDR_EL1 - Main ID Register
; Contains the primary processor identification metrics. Tells the hypervisor 
; the manufacturer (e.g., Implementer code for ARM, Apple, Ampere), the 
; specific processor core type (e.g., Cortex-A78), and architecture revision.
; -----------------------------------------------------------------------
%define MIDR_EL1            MIDR_EL1

; -----------------------------------------------------------------------
; MPIDR_EL1 - Multiprocessor Affinity Register
; Identifies the specific hardware core ID and cluster geometry (affinity 
; levels) within a multi-core processor layout. Used by your scheduler to 
; unique-map physical CPUs to guest virtual CPUs (vCPUs).
; -----------------------------------------------------------------------
%define MPIDR_EL1           MPIDR_EL1

; -----------------------------------------------------------------------
; ID_AA64PFR0_EL1 - AArch64 Processor Feature Register 0
; Reports supported top-level execution features in the hardware. Tells you 
; if the current core implements the EL2/EL3 levels, GIC CPU interface, 
; AdvSIMD/Floating-point units, and specific state tracking options.
; -----------------------------------------------------------------------
%define ID_AA64PFR0_EL1     ID_AA64PFR0_EL1

; -----------------------------------------------------------------------
; ID_AA64MMFR0_EL1 - AArch64 Memory Model Feature Register 0
; Reports memory management capabilities of the core. Tells your hypervisor 
; the maximum supported physical address width (e.g., 40-bit, 48-bit, 52-bit), 
; memory granule sizes (4KB, 16KB, 64KB), and Stage 2 memory translation states.
; -----------------------------------------------------------------------
%define ID_AA64MMFR0_EL1    ID_AA64MMFR0_EL1

; -----------------------------------------------------------------------
; ID_AA64ISAR0_EL1 - AArch64 Instruction Set Attribute Register 0
; Identifies supported hardware cryptographic extensions and instructions 
; implemented in the silicon, such as AES, SHA1, SHA2, SHA3, and SM4 acceleration.
; -----------------------------------------------------------------------
%define ID_AA64ISAR0_EL1    ID_AA64ISAR0_EL1

; =======================================================================
; ARM64 - PART 7: GUEST EL1 CONTEXT REGISTERS
; =======================================================================

; -----------------------------------------------------------------------
; SCTLR_EL1 - System Control Register (EL1)
; Controls the guest's own MMU, data/instruction caches, and alignment checks.
; Must be saved/restored to maintain the guest's operational state.
; -----------------------------------------------------------------------
%define SCTLR_EL1           SCTLR_EL1

; -----------------------------------------------------------------------
; TTBR0_EL1 / TTBR1_EL1 - Translation Table Base Registers (EL1)
; Hold the physical addresses of the guests own page tables (Stage 1).
; TTBR0 is typically user space and TTBR1 is kernel space within the VM.
; -----------------------------------------------------------------------
%define TTBR0_EL1           TTBR0_EL1
%define TTBR1_EL1           TTBR1_EL1

; -----------------------------------------------------------------------
; TCR_EL1 - Translation Control Register (EL1)
; Configures the guests Stage 1 MMU layout, including virtual address sizes,
; page granule sizes (4KB/16KB/64KB), and memory cacheability attributes.
; -----------------------------------------------------------------------
%define TCR_EL1             TCR_EL1

; -----------------------------------------------------------------------
; VBAR_EL1 - Vector Base Address Register (EL1)
; Holds the base virtual address of the guest OS exception vector table.
; Essential for routing kernel panics or interrupts inside the VM correctly.
; -----------------------------------------------------------------------
%define VBAR_EL1            VBAR_EL1

; -----------------------------------------------------------------------
; SP_EL1 / SP_EL0 - Stack Pointers (EL1 / EL0)
; The physical stack pointers used by the guest kernel and guest user space.
; Your assembly context switch must save these to prevent stack corruption.
; -----------------------------------------------------------------------
%define SP_EL1              SP_EL1
%define SP_EL0              SP_EL0

; =======================================================================
; ARM64 - PART 8: CACHE & TLB VIRTUALIZATION CONTROLS
; =======================================================================

; -----------------------------------------------------------------------
; TLBI ALLE2OS - TLB Invalidate All, EL2 Outer Shareable
; Architectural instruction operand used to invalidate all TLB entries 
; at the hypervisors own translation level (EL2) across all cores.
; -----------------------------------------------------------------------
%define TLBI_ALLE2OS        TLBI_ALLE2OS

; -----------------------------------------------------------------------
; TLBI VMALLS12E1OS - TLB Invalidate VM All Stage 1 and 2, EL1 Outer Shareable
; Invalidates all Stage 1 and Stage 2 TLB entries for the current Guest VM 
; context (matched by the current VMID in VTTBR_EL2) across the system.
; -----------------------------------------------------------------------
%define TLBI_VMALLS12E1OS   TLBI_VMALLS12E1OS

; -----------------------------------------------------------------------
; CLIDR_EL1 - Cache Level ID Register
; Reports the architecture and type of caches implemented at each level 
; (L1, L2, L3 Data/Instruction). Read by the hypervisor to allocate clean
; buffers and determine cash coherency boundaries during vCPU migration.
; -----------------------------------------------------------------------
%define CLIDR_EL1           CLIDR_EL1

; -----------------------------------------------------------------------
; CSSELR_EL1 - Cache Size Selection Register
; Selects the specific cache level and type (Data or Instruction) inspected 
; via the CCSIDR_EL1 register. Used during manual cache flushing routines.
; -----------------------------------------------------------------------
%define CSSELR_EL1          CSSELR_EL1

; -----------------------------------------------------------------------
; CCSIDR_EL1 - Cache Size ID Register
; Reports architectural parameters (such as line size, associativity, and 
; number of sets) for the cache level currently selected by CSSELR_EL1.
; -----------------------------------------------------------------------
%define CCSIDR_EL1          CCSIDR_EL1

; =======================================================================
; PART 9: BARE-METAL UART (SERIAL PORT DEBUGGING) FOR ARM64
; =======================================================================
%define UART0_BASE_ADDRESS  0x09000000  ; Base address for QEMU Virt Machine (or 0xFE201000 for RPi4)
%define UART_DR             0x000       ; Data Register (Write characters here to print to screen)
%define UART_FR             0x018       ; Flag Register (Check if the transmit buffer is full)

; =======================================================================
; PART 9: HCR_EL2 HYPERVISOR CONTROL BITMASKS FOR ARM64
; =======================================================================
%define HCR_VM              (1 << 0)    ; Virtualization Management Enable (Enforces Stage 2 MMU)
%define HCR_SWIO            (1 << 1)    ; Set Set/Way Invalidation Override
%define HCR_FMO             (1 << 3)    ; FIQ Mask Override (Routes physical FIQ interrupts to EL2)
%define HCR_IMO             (1 << 4)    ; IRQ Mask Override (Routes physical IRQ interrupts to EL2)
%define HCR_AMO             (1 << 5)    ; Asynchronous Abort Mask Override (Traps system errors to EL2)
%define HCR_TGE             (1 << 27)   ; Trap General Exceptions (Forces all EL0/EL1 traps directly to EL2)


; =======================================================================
; RISC-V - PART 1: HYPERVISOR STATUS & CONTROL CSRs
; =======================================================================

; -----------------------------------------------------------------------
; hstatus - Hypervisor Status Register (Address: 0x600)
; The most critical CSR in RISC-V H-Extension. It tracks the virtualization 
; state, controls whether the guest can execute certain privileged actions,
; and manages execution features when switching between the host and guest.
; -----------------------------------------------------------------------
%define CSR_HSTATUS         0x600

; -----------------------------------------------------------------------
; hedeleg - Hypervisor Exception Delegation Register (Address: 0x602)
; Allows the hypervisor to delegate specific synchronous exceptions (like 
; environment calls or instruction faults) directly to the VS-mode guest.
; This prevents unnecessary traps to the host, maximizing performance.
; -----------------------------------------------------------------------
%define CSR_HEDELEG         0x602

; -----------------------------------------------------------------------
; hideleg - Hypervisor Interrupt Delegation Register (Address: 0x603)
; Allows the hypervisor to delegate specific asynchronous interrupts (like
; guest software or timer interrupts) directly to the VS-mode guest, 
; bypasssing the host execution scheduler for faster processing.
; -----------------------------------------------------------------------
%define CSR_HIDELEG         0x603

; -----------------------------------------------------------------------
; hcounteren - Hypervisor Counter-Enable Register (Address: 0x606)
; Controls guest access to hardware performance counters and timers. 
; Clearing bits here will trap guest attempts to read hardware cycles,
; allowing the hypervisor to emulate or throttle performance tracking.
; -----------------------------------------------------------------------
%define CSR_HCOUNTEREN      0x606

; -----------------------------------------------------------------------
; hgeilen - Hypervisor Guest External Interrupt Level Enable (Address: 0x608)
; Configures and enables the target physical interrupt levels allocated 
; specifically for guest external interrupt routing within the hardware.
; -----------------------------------------------------------------------
%define CSR_HGEILEN         0x608

; =======================================================================
; RISC-V HYPERVISOR BIBLE - PART 2: TWO-STAGE MEMORY TRANSLATION & CONFIG
; =======================================================================

; -----------------------------------------------------------------------
; hgatp - Hypervisor Guest Address Translation and Protection (Address: 0x680)
; Holds the physical page number (PPN) of the G-stage (Stage 2) root page table
; and defines the translation mode (e.g., Sv39x4, Sv48x4) and VMID for the guest.
; Similar to the 'satp' register but enforces guest physical memory boundaries.
; -----------------------------------------------------------------------
%define CSR_HGATP           0x680

; -----------------------------------------------------------------------
; henvcfg - Hypervisor Environment Configuration Register (Address: 0x60A)
; Controls the execution environment capabilities for modes less privileged
; than HS-mode. Enables or disables hardware features like cache-block operations,
; memory-type extensions (Svpbmt), and pointer-masking inside the guest.
; -----------------------------------------------------------------------
%define CSR_HENVCFG         0x60A

; -----------------------------------------------------------------------
; hcontext - Hypervisor Mode Context Register (Address: 0x6A8)
; A scratchpad context register optionally used by the hypervisor or hardware
; tracing units to assist in tracking the active virtual machine thread identifier.
; -----------------------------------------------------------------------
%define CSR_HCONTEXT        0x6A8

; =======================================================================
; RISC-V HYPERVISOR BIBLE - PART 3: HYPERVISOR TRAP HANDLING
; =======================================================================

; -----------------------------------------------------------------------
; htval - Hypervisor Trap Value Register (Address: 0x643)
; Written automatically by hardware on a trap to HS-mode. For guest page faults,
; it holds the guest physical address ( Intermediate Physical Address / IPA) 
; that caused the exception, assisting the hypervisor in MMIO emulation.
; -----------------------------------------------------------------------
%define CSR_HTVAL           0x643

; -----------------------------------------------------------------------
; htinst - Hypervisor Trap Instruction Register (Address: 0x64A)
; Provides a transformed copy of the trapping instruction that caused the guest
; exit. This eliminates the need for your hypervisor to manually read and parse 
; the guests instruction memory cache, dramatically reducing exit latency.
; -----------------------------------------------------------------------
%define CSR_HTINST          0x64A

; -----------------------------------------------------------------------
; hstatus.GVA - Guest Virtual Address Flag (Part of hstatus bits)
; When a trap occurs, this flag indicates whether the value written into the 
; supervisor 'stval' register is a guest virtual address, verifying state validity.
; -----------------------------------------------------------------------
%define HSTATUS_GVA_BIT     6

; =======================================================================
; RISC-V HYPERVISOR BIBLE - PART 4: VIRTUAL INTERRUPT CONTROLS
; =======================================================================

; -----------------------------------------------------------------------
; hvip - Hypervisor Virtual Interrupt Pending Register (Address: 0x645)
; Used by the hypervisor to inject virtual supervisor interrupts (software, 
; timer, and external interrupts) directly into the guest VM. Writing bits 
; here forces the corresponding pending flags to trigger inside the VS-mode guest.
; -----------------------------------------------------------------------
%define CSR_HVIP            0x645

; -----------------------------------------------------------------------
; hip - Hypervisor Interrupt Pending Register (Address: 0x644)
; Reports all active physical and virtual interrupts currently pending for 
; the hypervisor domain. Allows the host to audit guest interrupt execution.
; -----------------------------------------------------------------------
%define CSR_HIP             0x644

; -----------------------------------------------------------------------
; hie - Hypervisor Interrupt Enable Register (Address: 0x604)
; Controls which physical and virtual interrupts are allowed to trap or execute 
; within the hypervisors own context.
; -----------------------------------------------------------------------
%define CSR_HIE             0x604

; =======================================================================
; RISC-V HYPERVISOR BIBLE - PART 5: VIRTUAL SUPERVISOR (VS-MODE) GUEST CONTEXT
; =======================================================================

; -----------------------------------------------------------------------
; vsstatus - Virtual Supervisor Status Register (Address: 0x200)
; Holds the virtual replica of the supervisor status register ('sstatus') 
; for the active guest VM. Tracks guest interrupt enables, privilege fields, 
; and extension states (Floating Point, Vector) for the virtualized context.
; -----------------------------------------------------------------------
%define CSR_VSSTATUS        0x200

; -----------------------------------------------------------------------
; vsie - Virtual Supervisor Interrupt Enable Register (Address: 0x204)
; Replicates the 'sie' register for the guest VM. Determines which virtualized 
; software, timer, or external interrupts the guest kernel has decided to unmask.
; -----------------------------------------------------------------------
%define CSR_VSIE            0x204

; -----------------------------------------------------------------------
; vstvec - Virtual Supervisor Trap Vector Base Address (Address: 0x205)
; Replicates the 'stvec' register for the guest. Holds the guest kernels 
; exception vector base physical/virtual jump table for handling inner VM traps.
; -----------------------------------------------------------------------
%define CSR_VSTVEC          0x205

; -----------------------------------------------------------------------
; vsscratch - Virtual Supervisor Scratch Register (Address: 0x240)
; Replicates the 'sscratch' register. Used by the guest kernel to hold 
; a context pointer to its own thread state structures during entry transitions.
; -----------------------------------------------------------------------
%define CSR_VSSCRATCH       0x240

; -----------------------------------------------------------------------
; vsepc - Virtual Supervisor Exception Program Counter (Address: 0x241)
; Replicates the 'sepc' register for the guest. Tracks the target instruction 
; execution pointer (PC) where the guest kernel handles its inner OS exceptions.
; -----------------------------------------------------------------------
%define CSR_VSEPC           0x241

; -----------------------------------------------------------------------
; vscause - Virtual Supervisor Cause Register (Address: 0x242)
; Replicates the 'scause' register for the guest. Tracks the exact reason 
; (interrupt vs exception code) for a trap occurring inside the guest OS domain.
; -----------------------------------------------------------------------
%define CSR_VSCAUSE         0x242

; -----------------------------------------------------------------------
; vstval - Virtual Supervisor Trap Value Register (Address: 0x243)
; Replicates the 'stval' register for the guest. Holds exception specific 
; information (such as bad memory addresses) generated entirely inside the VM.
; -----------------------------------------------------------------------
%define CSR_VSTVAL          0x243

; -----------------------------------------------------------------------
; vsip - Virtual Supervisor Interrupt Pending Register (Address: 0x244)
; Replicates the 'sip' register for the guest. Tracks active pending bits 
; for virtual software, timer, or external interrupts visible inside the VM.
; -----------------------------------------------------------------------
%define CSR_VSIP            0x244

; -----------------------------------------------------------------------
; vsatp - Virtual Supervisor Address Translation and Protection (Address: 0x280)
; Replicates the 'satp' register for the guest VM. Holds the root physical 
; page number (PPN) for the guest's Stage 1 page tables. Enforces the guest's 
; internal virtual memory mapping (e.g., Sv39, Sv48) inside the machine.
; -----------------------------------------------------------------------
%define CSR_VSATP           0x280

; =======================================================================
; RISC-V HYPERVISOR BIBLE - PART 6: ADVANCED INTERRUPTS (AIA) & TLB CONTROLS
; =======================================================================

; -----------------------------------------------------------------------
; hvictl - Hypervisor Virtual Interrupt Control Register (Address: 0x609)
; Part of the RISC-V Advanced Interrupt Architecture (AIA). Provides automated
; hardware assist for injecting, prioritizing, and masking guest external 
; interrupts, eliminating major software overhead in virtual routing.
; -----------------------------------------------------------------------
%define CSR_HVICTL          0x609

; -----------------------------------------------------------------------
; hvien - Hypervisor Virtual Interrupt Enable Register (Address: 0x605)
; Works alongside the AIA framework to selectively enable or disable specific
; virtual supervisor interrupts for the active virtual machine thread.
; -----------------------------------------------------------------------
%define CSR_HVIEN           0x605

; -----------------------------------------------------------------------
; hgeip - Hypervisor Guest External Interrupt Pending Register (Address: 0x607)
; Read-only register indicating which guest external interrupts are currently 
; pending hardware-level processing for each allocated virtual machine slot.
; -----------------------------------------------------------------------
%define CSR_HGEIP           0x607

; -----------------------------------------------------------------------
; HFENCE.VVMA - Hypervisor Fence Virtual VM Address Translation (Instruction Op)
; Assembly instruction operand used by the hypervisor to invalidate Stage 1 
; TLB entries for a specific guest VM context without touching host mappings.
; -----------------------------------------------------------------------
%define HFENCE_VVMA         HFENCE_VVMA

; -----------------------------------------------------------------------
; HFENCE.GVMA - Hypervisor Fence Guest Physical Address Translation (Instruction Op)
; Assembly instruction operand used by the hypervisor to flush Stage 2 (G-stage) 
; TLB cache lines. Mandatory after altering the 'hgatp' register to block memory leaks.
; -----------------------------------------------------------------------
%define HFENCE_GVMA         HFENCE_GVMA

; =======================================================================
; PART 7: PLATFORM-LEVEL INTERRUPT CONTROLLER (PLIC) FOR RISC-V
; =======================================================================
%define PLIC_BASE_ADDRESS   0x0C000000  ; Standard PLIC Base Address in RISC-V QEMU/Hardware architectures
%define PLIC_PRIORITY_BASE  0x00000000  ; Interrupt source priority registers
%define PLIC_PENDING_BASE   0x00001000  ; Interrupt source pending bits

; =======================================================================
; PART 8: HSTATUS & SSTATUS VIRTUALIZATION BITMASKS FOR RISC-V
; =======================================================================
%define HSTATUS_SPV         (1 << 7)    ; Supervisor Previous Virtualization mode (1 = Trap came from Virtual Guest)
%define HSTATUS_SPVP        (1 << 8)    ; Supervisor Previous Virtual Privilege (0 = Guest User, 1 = Guest Kernel)
%define SSTATUS_SIE         (1 << 1)    ; Supervisor Interrupt Enable (Controls host kernel interrupts)
%define SSTATUS_SPIE        (1 << 5)    ; Supervisor Previous Interrupt Enable

