# HyperCE - Leveraging Cheat Engine with VT-x Hypervisor for Enhanced Game Analysis

## Medthod

the kernel function MiReadWriteVirtualMemory call ObReferenceObjectByHandleWithTag to check the privilege.
so hooking ObReferenceObjectByHandleWithTag can let Cheat Engine acess any process despite proctection.
```
__int64 __fastcall MiReadWriteVirtualMemory(
        HANDLE Handle,
        char *a2,
        char *a3,
        size_t a4,
        unsigned __int64 a5,
        ACCESS_MASK DesiredAccess)
{
  __int64 v9; // rsi
  struct _KTHREAD *CurrentThread; // r14
  KPROCESSOR_MODE PreviousMode; // al
  _QWORD *v12; // rbx
  __int64 v13; // rcx
  NTSTATUS v14; // edi
  _KPROCESS *Process; // r10
  PVOID v16; // r14
  char *v17; // r9
  _KPROCESS *v18; // r8
  char *v19; // rdx
  _KPROCESS *v20; // rcx
  NTSTATUS v21; // eax
  int v22; // r10d
  KPROCESSOR_MODE v24; // [rsp+40h] [rbp-48h]
  __int64 v25; // [rsp+48h] [rbp-40h] BYREF
  PVOID Object[2]; // [rsp+50h] [rbp-38h] BYREF

  v9 = 0LL;
  Object[0] = 0LL;
  CurrentThread = KeGetCurrentThread();
  PreviousMode = CurrentThread->PreviousMode;
  v24 = PreviousMode;
  if ( PreviousMode )
  {
    if ( &a2[a4] < a2
      || (unsigned __int64)&a2[a4] > 0x7FFFFFFF0000LL
      || &a3[a4] < a3
      || (unsigned __int64)&a3[a4] > 0x7FFFFFFF0000LL )
    {
      return 3221225477LL;
    }
    v12 = (_QWORD *)a5;
    if ( a5 )
    {
      v13 = a5;
      if ( a5 >= 0x7FFFFFFF0000LL )
        v13 = 0x7FFFFFFF0000LL;
      *(_QWORD *)v13 = *(_QWORD *)v13;
    }
  }
  else
  {
    v12 = (_QWORD *)a5;
  }
  v25 = 0LL;
  v14 = 0;
  if ( a4 )
  {
    v14 = ObReferenceObjectByHandleWithTag(
            Handle,
            DesiredAccess,
            (POBJECT_TYPE)PsProcessType,
            PreviousMode,
            0x6D566D4Du,
            Object,
            0LL);
```
code:
https://github.com/oakboat/HyperCE/blob/e028a0124149f155af49d47cd290fa2c6fea395e/gbhv/ept.c#L522-L523

## Demo

first, openprocess without read privilege.
https://github.com/oakboat/HyperCE/blob/cc6b51a1f94e85ad804a2fd27513176ffdb2efd4/test/test.cpp#L89
Not HyperCE
![image](https://github.com/user-attachments/assets/b493d711-6f76-4167-9f76-ab6726603544)
With HyperCE
![7fd0c55aee9fa54db219817558e4c60](https://github.com/user-attachments/assets/7bab7d76-c463-42d0-a5ee-554e63487bd4)

## References

**[gbhv](https://github.com/Gbps/gbhv)**
