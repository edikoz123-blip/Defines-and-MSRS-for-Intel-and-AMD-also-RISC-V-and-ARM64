; =======================================================================
; ---------- ASSEMBLY GHOSTS CORE ARCHITECTURAL REGISTER DEFINES --------
; =======================================================================

; =======================================================================
; 1. CORE HARDWARE VIRTUALIZATION & EXTENDED FEATURES (AMD SVM / INTEL)
; =======================================================================
%define EFER_MSR            0xC0000080  ; Extended Feature Enable Register (NXE, SVME, LME)
%define VM_CR_MSR           0xC0010112  ; SVM Hardware Virtualization Configuration & Lock Register
%define VM_HSAVE_PA_MSR     0xC0010114  ; Host Save Area Physical Address (AMD SVM core requirement)

; =======================================================================
; 2. SIDE-CHANNEL ATTACK MITIGATIONS & SILICON ISOLATION SHIELDS
; =======================================================================
%define IA32_SPEC_CTRL      0x00000048  ; Speculative Execution Control (IBRS, STIBP, SSBD switches)
%define IA32_PRED_CMD       0x00000049  ; Prediction Command Trigger (IBPB branch history flush)
%define IA32_FLUSH_CMD      0x0000010B  ; L1 Data Cache Hard Flush Command Trigger (Meltdown shield)
%define IA32_ARCH_CAPABILITIES 0x0000010A ; Architectural Capabilities (Read rogue data mitigation flags)
%define IA32_TSX_CTRL       0x00000122  ; Intel TSX Transactional Execution Control (TAA mitigation)

; =======================================================================
; 3. CORE MEMORY TYPING, RANGE REGISTERS (MTRRs) & SYSTEM APIC
; =======================================================================
%define IA32_APIC_BASE      0x0000001B  ; Local APIC Base Physical Address and Enable/Disable register
%define IA32_MTRR_DEF_TYPE  0x000002FF  ; Memory Type Range Register Default Memory Access Type Frame
%define MSR_MTRRfix64k_00000 0x00000250 ; Fixed-Range MTRR mapping for low 64KB physical address block
%define MSR_MTRRcap         0x000000FE  ; MTRR Architectural Capabilities (Read supported memory types)

; =======================================================================
; 4. ADVANCED HARDWARE ENCRYPTION & ATTRIBUTES (AMD SEV / SEV-SNP)
; =======================================================================
%define MSR_SEV_STATUS      0xC0010131  ; AMD Secure Encrypted Virtualization Active Features Status
%define MSR_VCPU_ID         0xC001013A  ; AMD SEV-SNP Guest Virtual CPU Attestation Identifier
%define MSR_SEV_FEATURES    0xC001013E  ; AMD SEV Supported Security Features Extension Bitmap

; =======================================================================
; 5. CONTROL-FLOW ENFORCEMENT TECHNOLOGY (CET ANTI-ROP/ANTI-JOP)
; =======================================================================
%define IA32_U_CET          0x000006A0  ; Control-flow Enforcement Register - User Mode Restrictions
%define IA32_S_CET          0x000006A1  ; Control-flow Enforcement Register - Supervisor Kernel Mode
%define IA32_PL0_SSP        0x000006A4  ; Privilege Level 0 Shadow Stack Pointer Context Register
%define IA32_INTERRUPT_SSP_TABLE_ADDR 0x000006A8 ; Shadow Stack Pointer table address for hardware interrupts

; =======================================================================
; 6. CRITICAL LEGACY & SYSTEM KERNEL ENTRY/EXIT INTERFACES
; =======================================================================
%define IA32_STAR           0xC0000081  ; Legacy Ring 0/3 Segment Selectors Target Map for SYSCALL/SYSRET
%define IA32_LSTAR          0xC0000082  ; 64-bit Target RIP Vector for execution entry on SYSCALL instruction
%define IA32_CSTAR          0xC0000083  ; Compatibility Mode Target RIP Vector for SYSCALL execution
%define IA32_FMASK          0xC0000084  ; RFLAGS Bitmask to clear dynamically during SYSCALL transition
%define IA32_FS_BASE        0xC0000100  ; Linear base target physical mapping address for FS register
%define IA32_GS_BASE        0xC0000101  ; Linear base target physical mapping address for GS register
%define IA32_KERNEL_GS_BASE 0xC0000102  ; Swapped base target mapping address for OS Kernel via SWAPGS

; =======================================================================
; 7. INTEL LEGACY SYSTEM ENTER/EXIT CONTROL REGISTERS (SYSENTER)
; =======================================================================
%define IA32_SYSENTER_CS    0x00000174  ; Target Code Segment Selector for SYSENTER instruction
%define IA32_SYSENTER_ESP   0x00000175  ; Isolated Target Stack Pointer (ESP) for SYSENTER routine
%define IA32_SYSENTER_EIP   0x00000176  ; Target Execution Instruction Pointer (EIP) entry vector

; =======================================================================
; 8. PERFORMANCE MONITORING, HARDWARE COUNTERS & ARCHITECTURAL DEBUGGING
; =======================================================================
%define IA32_PERF_GLOBAL_CTRL 0x0000038F ; Global Performance Counter Execution Master Control Switch
%define IA32_PERF_STATUS    0x00000198  ; Silicon Operating Frequency and Voltage Status Register
%define IA32_DEBUGCTL       0x000001D9  ; Hardware Debug Control Interface (LBR, BTF, Branch Recording)
%define MSR_BR_FROM_IP      0x00000680  ; Last Branch Record (LBR) Source Execution Instruction Pointer
%define MSR_BR_TO_IP        0x000006C0  ; Last Branch Record (LBR) Destination Execution Target Vector

; =======================================================================
; 9. THERMAL PATROL, TEMPERATURE MANAGEMENT & ENHANCED POWER CONTROL
; =======================================================================
%define IA32_THERM_CONTROL  0x0000019A  ; Clock Modulation and Silicon Thermal Monitor Interface Mask
%define IA32_THERM_STATUS   0x0000019C  ; Digital Temperature Sensor Output and Max Limit Thresholds
%define IA32_MISC_ENABLE    0x000001A0  ; Miscellaneous Processor Features Enable Frame (XD, SpeedStep)
%define IA32_ENERGY_PERF_BIAS 0x000001B1 ; Hardware Energy Policy Optimization Preference Configuration

; =======================================================================
; 10. AMD ADVANCED SYSTEM ARCHITECTURE & EXCEPTION VECTOR EXTENSIONS
;========================================================================
%define MSR_AMD_PATCH_LEVEL 0x0000008B  ; Current Microcode Patch Level Revision (Read-Only validation)
%define MSR_HW_CR           0xC0010015  ; Hardware Configuration Register (TSC frequency lock parameters)
%define MSR_NB_CFG          0xC001001F  ; Northbridge Configuration Interface (Advanced memory profiling)
%define MSR_EXT_FEATURES    0xC0010058  ; Extended Exception Vector Configuration and Silicon Attributes

; =======================================================================
; 11. TLB HARDWARE MANAGEMENT & PROCESSOR CORE ISOLATION
; =======================================================================
%define IA32_PASID          0x000000D0  ; Process Address Space ID MSR (Enforces safe hardware context separation)
%define IA32_FLUSH_TLB      0x0000010E  ; Hardware Direct TLB Invalidation Trigger Register (Cache cleaner)

; =======================================================================
; 12. ADVANCED CORE TOPOLOGY & HYPER-THREADING CORRELATION CONTROL
; =======================================================================
%define IA32_CORE_CAPABILITIES 0x000000CF ; Architectural Processor Core Capabilities Frame (Read execution limits)
%define IA32_UMWAIT_CONTROL 0x000000E1  ; User Mode Wait Control MSR (Locks latency capabilities of low-level loops)

; =======================================================================
; 13. NESTED VIRTUALIZATION MASTER CONTROLS (ADVANCED AMD-V INJECTION)
; =======================================================================
%define MSR_VM_IGNNE        0xC0010115  ; SVM Ignore Numeric Error Mitigation Register (Legacy virtualization lock)
%define MSR_AMD_EXT_HW_CR   0xC0010015  ; AMD Extended Hardware Configuration (TSC frequency & SVM feature control)

; =======================================================================
; 14. TIME STAMP COUNTER (TSC) & ACCURATE TIMING CONTROL
; =======================================================================
%define IA32_TIME_STAMP_COUNTER 0x00000010 ; Raw Hardware Cycle Counter (Target for TSC manipulation)
%define IA32_TSC_ADJUST     0x0000003B  ; TSC Offset Adjustment Frame (Used to hide hypervisor latency)
%define IA32_TSC_DEADLINE   0x000006E0  ; Local APIC TSC Deadline Mode Timer Control Register

; =======================================================================
; 15. MACHINE CHECK ARCHITECTURE (MCA ENGINE - PHYSICAL FAULT DETECTION)
; =======================================================================
%define IA32_MCG_CAP        0x00000179  ; Global Machine Check Capabilities (Read hardware error banks)
%define IA32_MCG_STATUS     0x0000017A  ; Global Machine Check Status (Validates active silicon faults)
%define IA32_MCG_CTL        0x0000017B  ; Global Machine Check Control Interface (Master exception switch)
%define IA32_MC0_CTL        0x00000400  ; Hardware Error Bank 0 Control Register (First silicon unit)
%define IA32_MC0_STATUS     0x00000401  ; Hardware Error Bank 0 Status Frame (Read active hardware faults)

; =======================================================================
; 16. ADVANCED HARDWARE TRACING & PROCESSOR TRACE FRAMEWORK (INTEL/AMD)
; =======================================================================
%define IA32_RTIT_CTL       0x00000570  ; Real-Time Instruction Trace Control (Silicon execution logging)
%define IA32_RTIT_STATUS    0x00000571  ; Real-Time Instruction Trace Status (Monitor tracer execution state)
%define IA32_RTIT_OUTPUT_BASE 0x00000560 ; Direct hardware tracing target physical memory lane allocation

; =======================================================================
; 17. MSR PERMISSIONS MAPS (MSRP ARCHITECTURE FOR HARDWARE BLOCKING)
; =======================================================================
; These are used inside the VMCB Control Area to block Guest access to MSRs.
%define MSRP_BASE_ADDRESS   0x00010000  ; Recommended 8KB physical buffer boundary for MSRP
%define MSRP_OFFSET_DEVICE  0x00002004  ; VMCB Control area byte pointer anchoring the MSRP layout

; =======================================================================
; 18. AMD INTERNALS, DECODE CONFIGURATION & SILICON SPECULATION DEFENSE
; =======================================================================
%define MSR_DE_CFG          0xC0011029  ; Decode Configuration Register (Used to patch execution bugs like Zenbleed)
%define MSR_LS_CFG          0xC0011020  ; Load-Store Configuration Frame (Controls speculative execution serialization)
%define MSR_IC_CFG          0xC0011021  ; Instruction Cache Configuration (Silicon execution engine hardening)

; =======================================================================
; 19. I/O VIRTUALIZATION TECHNOLOGY & HARDWARE DMA PROTECTION (IOMMU)
; =======================================================================
%define MSR_IOMMU_BASE      0xC0010074  ; IOMMU Base Address Register (Controls hardware DMA mapping safety)
%define MSR_IOMMU_CONTROL   0xC0010075  ; IOMMU Execution Control Frame (Locks Direct Memory Access from external chips)

; =======================================================================
; 20. ARCHITECTURAL EXTENDED SEGMENTATION BASE POINTERS (LONG MODE)
; =======================================================================
%define IA32_TSC_AUX        0xC0000103  ; Auxiliary Time Stamp Counter (Used by RDTSCP instruction for Core ID injection)

; =======================================================================
; 21. ADVANCED CACHE ALLOCATION TECHNOLOGY (CAT ARCHITECTURE)
; =======================================================================
%define IA32_L3_QOS_MASK_0  0x00000C90  ; L3 Cache Allocation Mask 0 (Isolates Host cache lanes from Guest)
%define IA32_L2_QOS_MASK_0  0x00000D10  ; L2 Cache Allocation Mask 0 (Strict hardware core cache partitioning)

; =======================================================================
; 22. HARDWARE HARDENING & INDEPENDENT OVERFLOW DEFENSES
; =======================================================================
%define IA32_PRED_CTRL      0x0000004C  ; Prediction Control Register (Enforces hardware-level branch isolation)
%define IA32_GDT_ALIGN_LOCK 0x000002E0  ; Architectural Alignment Lock MSR (Silicon-level protection against #GP)
