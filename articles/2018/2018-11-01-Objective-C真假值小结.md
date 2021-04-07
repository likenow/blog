---
title: Objective-C真假值小结
date: 2018-11-01 20:37:15
categories:
- OC
tags:
- OC
---



## 真假值

| Name           | Typedef         | Header     | True Value       | False Value       |
| -------------- | --------------- | ---------- | ---------------- | ----------------- |
| `BOOL`         | `signed char`   | objc.h     | `YES`            | `NO`              |
| `bool`         | `_Bool` (`int`) | stdbool.h  | `true`           | `false`           |
| `Boolean`      | `unsigned char` | MacTypes.h | `TRUE`           | `FALSE`           |
| `CFBooleanRef` | `struct`        | CFNumber.h | `kCFBooleanTrue` | `kCFBooleanFalse` |



在Objective-C中，`BOOL`其实是**宏定义**，在`objc.h`头文件的源代码中它被定义为`signed char`类型，见代码77-84行。（注：在`64位`的`iPhone`平台以及`iWatch`平台下，`BOOL`等价于`bool`，见代码77-79行）

在Objective-C中，广泛使用的`YES`和`NO`也只是`signed char`类型的`1`和`0`，见代码88-94行。

<!--more-->

## 撸码容易遇到的坑

上述，Objective-C中的 `BOOL` 是 `signed char`类型，因此 `BOOL` 类型的值范围是 `-128-127` 不只是 `1(YES)/0(NO)`，其中`0`表示值为假，其他都为真。换句话说，`BOOL` 类型的真值不一定是 `1`。所以应当注意

- 避免与真值`YES`直接比较
- `bool` 类型值可以与` true`直接进行比较
- `BOOL` `bool` `Boolean `  类型间不要随便转换





## 参考代码

### objc.h

```objective-c
/*
 * Copyright (c) 1999-2007 Apple Inc.  All Rights Reserved.
 * 
 * @APPLE_LICENSE_HEADER_START@
 * 
 * This file contains Original Code and/or Modifications of Original Code
 * as defined in and that are subject to the Apple Public Source License
 * Version 2.0 (the 'License'). You may not use this file except in
 * compliance with the License. Please obtain a copy of the License at
 * http://www.opensource.apple.com/apsl/ and read it before using this
 * file.
 * 
 * The Original Code and all software distributed under the License are
 * distributed on an 'AS IS' basis, WITHOUT WARRANTY OF ANY KIND, EITHER
 * EXPRESS OR IMPLIED, AND APPLE HEREBY DISCLAIMS ALL SUCH WARRANTIES,
 * INCLUDING WITHOUT LIMITATION, ANY WARRANTIES OF MERCHANTABILITY,
 * FITNESS FOR A PARTICULAR PURPOSE, QUIET ENJOYMENT OR NON-INFRINGEMENT.
 * Please see the License for the specific language governing rights and
 * limitations under the License.
 * 
 * @APPLE_LICENSE_HEADER_END@
 */
/*
 *	objc.h
 *	Copyright 1988-1996, NeXT Software, Inc.
 */

#ifndef _OBJC_OBJC_H_
#define _OBJC_OBJC_H_

#include <sys/types.h>      // for __DARWIN_NULL
#include <Availability.h>
#include <objc/objc-api.h>
#include <stdbool.h>

#if !OBJC_TYPES_DEFINED
/// An opaque type that represents an Objective-C class.
typedef struct objc_class *Class;

/// Represents an instance of a class.
struct objc_object {
    Class _Nonnull isa  OBJC_ISA_AVAILABILITY;
};

/// A pointer to an instance of a class.
typedef struct objc_object *id;
#endif

/// An opaque type that represents a method selector.
typedef struct objc_selector *SEL;

/// A pointer to the function of a method implementation. 
#if !OBJC_OLD_DISPATCH_PROTOTYPES
typedef void (*IMP)(void /* id, SEL, ... */ ); 
#else
typedef id _Nullable (*IMP)(id _Nonnull, SEL _Nonnull, ...); 
#endif

/// Type to represent a boolean value.

#if defined(__OBJC_BOOL_IS_BOOL)
    // Honor __OBJC_BOOL_IS_BOOL when available.
#   if __OBJC_BOOL_IS_BOOL
#       define OBJC_BOOL_IS_BOOL 1
#   else
#       define OBJC_BOOL_IS_BOOL 0
#   endif
#else
    // __OBJC_BOOL_IS_BOOL not set.
#   if TARGET_OS_OSX || 0 || (TARGET_OS_IOS && !__LP64__ && !__ARM_ARCH_7K)
#      define OBJC_BOOL_IS_BOOL 0
#   else
#      define OBJC_BOOL_IS_BOOL 1
#   endif
#endif

#if OBJC_BOOL_IS_BOOL
    typedef bool BOOL;
#else
#   define OBJC_BOOL_IS_CHAR 1
    typedef signed char BOOL; 
    // BOOL is explicitly signed so @encode(BOOL) == "c" rather than "C" 
    // even if -funsigned-char is used.
#endif

#define OBJC_BOOL_DEFINED

#if __has_feature(objc_bool)
#define YES __objc_yes
#define NO  __objc_no
#else
#define YES ((BOOL)1)
#define NO  ((BOOL)0)
#endif

#ifndef Nil
# if __has_feature(cxx_nullptr)
#   define Nil nullptr
# else
#   define Nil __DARWIN_NULL
# endif
#endif

#ifndef nil
# if __has_feature(cxx_nullptr)
#   define nil nullptr
# else
#   define nil __DARWIN_NULL
# endif
#endif

#ifndef __strong
# if !__has_feature(objc_arc)
#   define __strong /* empty */
# endif
#endif

#ifndef __unsafe_unretained
# if !__has_feature(objc_arc)
#   define __unsafe_unretained /* empty */
# endif
#endif

#ifndef __autoreleasing
# if !__has_feature(objc_arc)
#   define __autoreleasing /* empty */
# endif
#endif


/** 
 * Returns the name of the method specified by a given selector.
 * 
 * @param sel A pointer of type \c SEL. Pass the selector whose name you wish to determine.
 * 
 * @return A C string indicating the name of the selector.
 */
OBJC_EXPORT const char * _Nonnull sel_getName(SEL _Nonnull sel)
    OBJC_AVAILABLE(10.0, 2.0, 9.0, 1.0, 2.0);

/** 
 * Registers a method with the Objective-C runtime system, maps the method 
 * name to a selector, and returns the selector value.
 * 
 * @param str A pointer to a C string. Pass the name of the method you wish to register.
 * 
 * @return A pointer of type SEL specifying the selector for the named method.
 * 
 * @note You must register a method name with the Objective-C runtime system to obtain the
 *  method’s selector before you can add the method to a class definition. If the method name
 *  has already been registered, this function simply returns the selector.
 */
OBJC_EXPORT SEL _Nonnull sel_registerName(const char * _Nonnull str)
    OBJC_AVAILABLE(10.0, 2.0, 9.0, 1.0, 2.0);

/** 
 * Returns the class name of a given object.
 * 
 * @param obj An Objective-C object.
 * 
 * @return The name of the class of which \e obj is an instance.
 */
OBJC_EXPORT const char * _Nonnull object_getClassName(id _Nullable obj)
    OBJC_AVAILABLE(10.0, 2.0, 9.0, 1.0, 2.0);

/** 
 * Returns a pointer to any extra bytes allocated with an instance given object.
 * 
 * @param obj An Objective-C object.
 * 
 * @return A pointer to any extra bytes allocated with \e obj. If \e obj was
 *   not allocated with any extra bytes, then dereferencing the returned pointer is undefined.
 * 
 * @note This function returns a pointer to any extra bytes allocated with the instance
 *  (as specified by \c class_createInstance with extraBytes>0). This memory follows the
 *  object's ordinary ivars, but may not be adjacent to the last ivar.
 * @note The returned pointer is guaranteed to be pointer-size aligned, even if the area following
 *  the object's last ivar is less aligned than that. Alignment greater than pointer-size is never
 *  guaranteed, even if the area following the object's last ivar is more aligned than that.
 * @note In a garbage-collected environment, the memory is scanned conservatively.
 */
OBJC_EXPORT void * _Nullable object_getIndexedIvars(id _Nullable obj)
    OBJC_AVAILABLE(10.0, 2.0, 9.0, 1.0, 2.0)
    OBJC_ARC_UNAVAILABLE;

/** 
 * Identifies a selector as being valid or invalid.
 * 
 * @param sel The selector you want to identify.
 * 
 * @return YES if selector is valid and has a function implementation, NO otherwise. 
 * 
 * @warning On some platforms, an invalid reference (to invalid memory addresses) can cause
 *  a crash. 
 */
OBJC_EXPORT BOOL sel_isMapped(SEL _Nonnull sel)
    OBJC_AVAILABLE(10.0, 2.0, 9.0, 1.0, 2.0);

/** 
 * Registers a method name with the Objective-C runtime system.
 * 
 * @param str A pointer to a C string. Pass the name of the method you wish to register.
 * 
 * @return A pointer of type SEL specifying the selector for the named method.
 * 
 * @note The implementation of this method is identical to the implementation of \c sel_registerName.
 * @note Prior to OS X version 10.0, this method tried to find the selector mapped to the given name
 *  and returned \c NULL if the selector was not found. This was changed for safety, because it was
 *  observed that many of the callers of this function did not check the return value for \c NULL.
 */
OBJC_EXPORT SEL _Nonnull sel_getUid(const char * _Nonnull str)
    OBJC_AVAILABLE(10.0, 2.0, 9.0, 1.0, 2.0);

typedef const void* objc_objectptr_t;


// Obsolete ARC conversions.

OBJC_EXPORT id _Nullable objc_retainedObject(objc_objectptr_t _Nullable obj)
#if !OBJC_DECLARE_SYMBOLS
    OBJC_UNAVAILABLE("use CFBridgingRelease() or a (__bridge_transfer id) cast instead")
#endif
    ;
OBJC_EXPORT id _Nullable objc_unretainedObject(objc_objectptr_t _Nullable obj)
#if !OBJC_DECLARE_SYMBOLS
    OBJC_UNAVAILABLE("use a (__bridge id) cast instead")
#endif
    ;
OBJC_EXPORT objc_objectptr_t _Nullable objc_unretainedPointer(id _Nullable obj)
#if !OBJC_DECLARE_SYMBOLS
    OBJC_UNAVAILABLE("use a __bridge cast instead")
#endif
    ;


#if !__OBJC2__

// The following declarations are provided here for source compatibility.

#if defined(__LP64__)
    typedef long arith_t;
    typedef unsigned long uarith_t;
#   define ARITH_SHIFT 32
#else
    typedef int arith_t;
    typedef unsigned uarith_t;
#   define ARITH_SHIFT 16
#endif

typedef char *STR;

#define ISSELECTOR(sel) sel_isMapped(sel)
#define SELNAME(sel)	sel_getName(sel)
#define SELUID(str)	sel_getUid(str)
#define NAMEOF(obj)     object_getClassName(obj)
#define IV(obj)         object_getIndexedIvars(obj)

#endif

#endif  /* _OBJC_OBJC_H_ */

```

### stdbool.h

```c
/*===---- stdbool.h - Standard header for booleans -------------------------===
 *
 * Copyright (c) 2008 Eli Friedman
 *
 * Permission is hereby granted, free of charge, to any person obtaining a copy
 * of this software and associated documentation files (the "Software"), to deal
 * in the Software without restriction, including without limitation the rights
 * to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
 * copies of the Software, and to permit persons to whom the Software is
 * furnished to do so, subject to the following conditions:
 *
 * The above copyright notice and this permission notice shall be included in
 * all copies or substantial portions of the Software.
 *
 * THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
 * IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
 * FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
 * AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
 * LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
 * OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
 * THE SOFTWARE.
 *
 *===-----------------------------------------------------------------------===
 */

#ifndef __STDBOOL_H
#define __STDBOOL_H

/* Don't define bool, true, and false in C++, except as a GNU extension. */
#ifndef __cplusplus
#define bool _Bool
#define true 1
#define false 0
#elif defined(__GNUC__) && !defined(__STRICT_ANSI__)
/* Define _Bool as a GNU extension. */
#define _Bool bool
#if __cplusplus < 201103L
/* For C++98, define bool, false, true as a GNU extension. */
#define bool  bool
#define false false
#define true  true
#endif
#endif

#define __bool_true_false_are_defined 1

#endif /* __STDBOOL_H */

```



###  

### MacTypes.h

```objective-c
/*
 * Copyright (c) 1985-2011 by Apple Inc.. All rights reserved.
 *
 * @APPLE_LICENSE_HEADER_START@
 * 
 * This file contains Original Code and/or Modifications of Original Code
 * as defined in and that are subject to the Apple Public Source License
 * Version 2.0 (the 'License'). You may not use this file except in
 * compliance with the License. Please obtain a copy of the License at
 * http://www.opensource.apple.com/apsl/ and read it before using this
 * file.
 * 
 * The Original Code and all software distributed under the License are
 * distributed on an 'AS IS' basis, WITHOUT WARRANTY OF ANY KIND, EITHER
 * EXPRESS OR IMPLIED, AND APPLE HEREBY DISCLAIMS ALL SUCH WARRANTIES,
 * INCLUDING WITHOUT LIMITATION, ANY WARRANTIES OF MERCHANTABILITY,
 * FITNESS FOR A PARTICULAR PURPOSE, QUIET ENJOYMENT OR NON-INFRINGEMENT.
 * Please see the License for the specific language governing rights and
 * limitations under the License.
 * 
 * @APPLE_LICENSE_HEADER_END@
 */
 
/*
     File:       MacTypes.h
 
     Contains:   Basic Macintosh data types.
 
     Version:    CarbonCore-769~1
  
     Bugs?:      For bug reports, consult the following page on
                 the World Wide Web:
 
                     http://developer.apple.com/bugreporter/
 
*/
#ifndef __MACTYPES__
#define __MACTYPES__

#ifndef __CONDITIONALMACROS__
#include <ConditionalMacros.h>
#endif

#include <stdbool.h>

#include <sys/types.h>

#include <Availability.h>

#if PRAGMA_ONCE
#pragma once
#endif

#ifdef __cplusplus
extern "C" {
#endif

#pragma pack(push, 2)


/*
        CarbonCore Deprecation flags.

     Certain Carbon API functions are deprecated in 10.3 and later
      systems.  These will produce a warning when compiling on 10.3.

        Other functions and constants do not produce meaningful
        results when building Carbon for Mac OS X.  For these
      functions, no-op macros are provided, but only when the
        ALLOW_OBSOLETE_CARBON flag is defined to be 0: eg
      -DALLOW_OBSOLETE_CARBON=0.
*/

#if  ! defined(ALLOW_OBSOLETE_CARBON) || ! ALLOW_OBSOLETE_CARBON

#define ALLOW_OBSOLETE_CARBON_MACMEMORY        0
#define ALLOW_OBSOLETE_CARBON_OSUTILS     0

#else

#define ALLOW_OBSOLETE_CARBON_MACMEMORY       1       /* Removes obsolete constants; turns HLock/HUnlock into no-op macros */
#define ALLOW_OBSOLETE_CARBON_OSUTILS       1       /* Removes obsolete structures */

#endif

#ifndef NULL
#define NULL    __DARWIN_NULL
#endif /* ! NULL */
#ifndef nil
  #if defined(__has_feature) 
    #if __has_feature(cxx_nullptr)
      #define nil nullptr
    #else
      #define nil __DARWIN_NULL
    #endif
  #else
    #define nil __DARWIN_NULL
  #endif
#endif

/********************************************************************************

    Base integer types for all target OS's and CPU's
    
        UInt8            8-bit unsigned integer 
        SInt8            8-bit signed integer
        UInt16          16-bit unsigned integer 
        SInt16          16-bit signed integer           
        UInt32          32-bit unsigned integer 
        SInt32          32-bit signed integer   
        UInt64          64-bit unsigned integer 
        SInt64          64-bit signed integer   

*********************************************************************************/
typedef unsigned char                   UInt8;
typedef signed char                     SInt8;
typedef unsigned short                  UInt16;
typedef signed short                    SInt16;

#if __LP64__
typedef unsigned int                    UInt32;
typedef signed int                      SInt32;
#else
typedef unsigned long                   UInt32;
typedef signed long                     SInt32;
#endif

/* avoid redeclaration if libkern/OSTypes.h */
#ifndef _OS_OSTYPES_H
#if TARGET_RT_BIG_ENDIAN
struct wide {
  SInt32              hi;
  UInt32              lo;
};
typedef struct wide                     wide;
struct UnsignedWide {
  UInt32              hi;
  UInt32              lo;
};
typedef struct UnsignedWide             UnsignedWide;
#else
struct wide {
  UInt32              lo;
  SInt32              hi;
};
typedef struct wide                     wide;
struct UnsignedWide {
  UInt32              lo;
  UInt32              hi;
};
typedef struct UnsignedWide             UnsignedWide;
#endif  /* TARGET_RT_BIG_ENDIAN */

#endif

#if TYPE_LONGLONG
/*
  Note:   wide and UnsignedWide must always be structs for source code
           compatibility. On the other hand UInt64 and SInt64 can be
          either a struct or a long long, depending on the compiler.
         
           If you use UInt64 and SInt64 you should do all operations on 
          those data types through the functions/macros in Math64.h.  
           This will assure that your code compiles with compilers that
           support long long and those that don't.
            
           The MS Visual C/C++ compiler uses __int64 instead of long long. 
*/
    #if defined(_MSC_VER) && !defined(__MWERKS__) && defined(_M_IX86)
      typedef   signed __int64                SInt64;
        typedef unsigned __int64                UInt64;
    #else
      typedef   signed long long              SInt64;
        typedef unsigned long long              UInt64;
    #endif
#else


typedef wide                            SInt64;
typedef UnsignedWide                    UInt64;
#endif  /* TYPE_LONGLONG */

/********************************************************************************

    Base fixed point types 
    
        Fixed           16-bit signed integer plus 16-bit fraction
        UnsignedFixed   16-bit unsigned integer plus 16-bit fraction
        Fract           2-bit signed integer plus 30-bit fraction
        ShortFixed      8-bit signed integer plus 8-bit fraction
        
*********************************************************************************/
typedef SInt32                          Fixed;
typedef Fixed *                         FixedPtr;
typedef SInt32                          Fract;
typedef Fract *                         FractPtr;
typedef UInt32                          UnsignedFixed;
typedef UnsignedFixed *                 UnsignedFixedPtr;
typedef short                           ShortFixed;
typedef ShortFixed *                    ShortFixedPtr;


/********************************************************************************

    Base floating point types 
    
        Float32         32 bit IEEE float:  1 sign bit, 8 exponent bits, 23 fraction bits
        Float64         64 bit IEEE float:  1 sign bit, 11 exponent bits, 52 fraction bits  
        Float80         80 bit MacOS float: 1 sign bit, 15 exponent bits, 1 integer bit, 63 fraction bits
        Float96         96 bit 68881 float: 1 sign bit, 15 exponent bits, 16 pad bits, 1 integer bit, 63 fraction bits
        
    Note: These are fixed size floating point types, useful when writing a floating
          point value to disk.  If your compiler does not support a particular size 
          float, a struct is used instead.
          Use of of the NCEG types (e.g. double_t) or an ANSI C type (e.g. double) if
          you want a floating point representation that is natural for any given
          compiler, but might be a different size on different compilers.

*********************************************************************************/
typedef float               Float32;
typedef double              Float64;
struct Float80 {
    SInt16  exp;
    UInt16  man[4];
};
typedef struct Float80 Float80;

struct Float96 {
    SInt16  exp[2];     /* the second 16-bits are undefined */
    UInt16  man[4];
};
typedef struct Float96 Float96;
struct Float32Point {
    Float32             x;
    Float32             y;
};
typedef struct Float32Point Float32Point;

/********************************************************************************

    MacOS Memory Manager types
    
        Ptr             Pointer to a non-relocatable block
        Handle          Pointer to a master pointer to a relocatable block
        Size            The number of bytes in a block (signed for historical reasons)
        
*********************************************************************************/
typedef char *                          Ptr;
typedef Ptr *                           Handle;
typedef long                            Size;

/********************************************************************************

    Higher level basic types
    
        OSErr                   16-bit result error code
        OSStatus                32-bit result error code
        LogicalAddress          Address in the clients virtual address space
        ConstLogicalAddress     Address in the clients virtual address space that will only be read
        PhysicalAddress         Real address as used on the hardware bus
        BytePtr                 Pointer to an array of bytes
        ByteCount               The size of an array of bytes
        ByteOffset              An offset into an array of bytes
        ItemCount               32-bit iteration count
        OptionBits              Standard 32-bit set of bit flags
        PBVersion               ?
        Duration                32-bit millisecond timer for drivers
        AbsoluteTime            64-bit clock
        ScriptCode              A particular set of written characters (e.g. Roman vs Cyrillic) and their encoding
        LangCode                A particular language (e.g. English), as represented using a particular ScriptCode
        RegionCode              Designates a language as used in a particular region (e.g. British vs American
                                English) together with other region-dependent characteristics (e.g. date format)
        FourCharCode            A 32-bit value made by packing four 1 byte characters together
        OSType                  A FourCharCode used in the OS and file system (e.g. creator)
        ResType                 A FourCharCode used to tag resources (e.g. 'DLOG')
        
*********************************************************************************/
typedef SInt16                          OSErr;
typedef SInt32                          OSStatus;
typedef void *                          LogicalAddress;
typedef const void *                    ConstLogicalAddress;
typedef void *                          PhysicalAddress;
typedef UInt8 *                         BytePtr;
typedef unsigned long                   ByteCount;
typedef unsigned long                   ByteOffset;
typedef SInt32                          Duration;
typedef UnsignedWide                    AbsoluteTime;
typedef UInt32                          OptionBits;
typedef unsigned long                   ItemCount;
typedef UInt32                          PBVersion;
typedef SInt16                          ScriptCode;
typedef SInt16                          LangCode;
typedef SInt16                          RegionCode;
typedef UInt32                          FourCharCode;
typedef FourCharCode                    OSType;
typedef FourCharCode                    ResType;
typedef OSType *                        OSTypePtr;
typedef ResType *                       ResTypePtr;
/********************************************************************************

    Boolean types and values
    
        Boolean         Mac OS historic type, sizeof(Boolean)==1
        bool            Defined in stdbool.h, ISO C/C++ standard type
        false           Now defined in stdbool.h
        true            Now defined in stdbool.h
        
*********************************************************************************/
typedef unsigned char                   Boolean;
/********************************************************************************

    Function Pointer Types
    
        ProcPtr                 Generic pointer to a function
        Register68kProcPtr      Pointer to a 68K function that expects parameters in registers
        UniversalProcPtr        Pointer to classic 68K code or a RoutineDescriptor
        
        ProcHandle              Pointer to a ProcPtr
        UniversalProcHandle     Pointer to a UniversalProcPtr
        
*********************************************************************************/
typedef CALLBACK_API_C( long , ProcPtr )(void);
typedef CALLBACK_API( void , Register68kProcPtr )(void);
#if TARGET_RT_MAC_CFM
/*  The RoutineDescriptor structure is defined in MixedMode.h */
typedef struct RoutineDescriptor *UniversalProcPtr;
#else
typedef ProcPtr                         UniversalProcPtr;
#endif  /* TARGET_RT_MAC_CFM */

typedef ProcPtr *                       ProcHandle;
typedef UniversalProcPtr *              UniversalProcHandle;
/********************************************************************************

    RefCon Types
    
        For access to private data in callbacks, etc.; refcons are generally
        used as a pointer to something, but in the 32-bit world refcons in
        different APIs have had various types: pointer, unsigned scalar, and
        signed scalar. The RefCon types defined here support the current 32-bit
        usage but provide normalization to pointer types for 64-bit.
        
        PRefCon is preferred for new APIs; URefCon and SRefCon are primarily
        for compatibility with existing APIs.
        
*********************************************************************************/
typedef void *                          PRefCon;
#if __LP64__
typedef void *                          URefCon;
typedef void *                          SRefCon;
#else
typedef UInt32                          URefCon;
typedef SInt32                          SRefCon;
#endif  /* __LP64__ */

/********************************************************************************

    Common Constants
    
        noErr                   OSErr: function performed properly - no error
        kNilOptions             OptionBits: all flags false
        kInvalidID              KernelID: NULL is for pointers as kInvalidID is for ID's
        kVariableLengthArray    array bounds: variable length array

    Note: kVariableLengthArray was used in array bounds to specify a variable length array,
          usually the last field in a struct.  Now that the C language supports 
		  the concept of flexible array members, you can instead use: 
		
		struct BarList
		{
			short	listLength;
			Bar		elements[];
		};

		However, this changes the semantics somewhat, as sizeof( BarList ) contains
		no space for any of the elements, so to allocate a list with space for
		the count elements

		struct BarList* l = (struct BarList*) malloc( sizeof(BarList) + count * sizeof(Bar) );
        
*********************************************************************************/
enum {
  noErr                         = 0
};

enum {
  kNilOptions                   = 0
};

#define kInvalidID   0
enum {
  kVariableLengthArray  
#ifdef __has_extension
   #if __has_extension(enumerator_attributes)
		__attribute__((deprecated))  
	#endif
#endif
  = 1
};

enum {
  kUnknownType                  = 0x3F3F3F3F /* "????" QuickTime 3.0: default unknown ResType or OSType */
};



/********************************************************************************

    String Types and Unicode Types
    
        UnicodeScalarValue,     A complete Unicode character in UTF-32 format, with
        UTF32Char               values from 0 through 0x10FFFF (excluding the surrogate
                                range 0xD800-0xDFFF and certain disallowed values).

        UniChar,                A 16-bit Unicode code value in the default UTF-16 format.
        UTF16Char               UnicodeScalarValues 0-0xFFFF are expressed in UTF-16
                                format using a single UTF16Char with the same value.
                                UnicodeScalarValues 0x10000-0x10FFFF are expressed in
                                UTF-16 format using a pair of UTF16Chars - one in the
                                high surrogate range (0xD800-0xDBFF) followed by one in
                                the low surrogate range (0xDC00-0xDFFF). All of the
                                characters defined in Unicode versions through 3.0 are
                                in the range 0-0xFFFF and can be expressed using a single
                                UTF16Char, thus the term "Unicode character" generally
                                refers to a UniChar = UTF16Char.

        UTF8Char                An 8-bit code value in UTF-8 format. UnicodeScalarValues
                                0-0x7F are expressed in UTF-8 format using one UTF8Char
                                with the same value. UnicodeScalarValues above 0x7F are
                                expressed in UTF-8 format using 2-4 UTF8Chars, all with
                                values in the range 0x80-0xF4 (UnicodeScalarValues
                                0x100-0xFFFF use two or three UTF8Chars,
                                UnicodeScalarValues 0x10000-0x10FFFF use four UTF8Chars).

        UniCharCount            A count of UTF-16 code values in an array or buffer.

        StrNNN                  Pascal string holding up to NNN bytes
        StringPtr               Pointer to a pascal string
        StringHandle            Pointer to a StringPtr
        ConstStringPtr          Pointer to a read-only pascal string
        ConstStrNNNParam        For function parameters only - means string is const
        
        CStringPtr              Pointer to a C string           (in C:  char*)
        ConstCStringPtr         Pointer to a read-only C string (in C:  const char*)
        
    Note: The length of a pascal string is stored as the first byte.
          A pascal string does not have a termination byte.
          A pascal string can hold at most 255 bytes of data.
          The first character in a pascal string is offset one byte from the start of the string. 
          
          A C string is terminated with a byte of value zero.  
          A C string has no length limitation.
          The first character in a C string is the zeroth byte of the string. 
          
        
*********************************************************************************/
typedef UInt32                          UnicodeScalarValue;
typedef UInt32                          UTF32Char;
typedef UInt16                          UniChar;
typedef UInt16                          UTF16Char;
typedef UInt8                           UTF8Char;
typedef UniChar *                       UniCharPtr;
typedef unsigned long                   UniCharCount;
typedef UniCharCount *                  UniCharCountPtr;
typedef unsigned char                   Str255[256];
typedef unsigned char                   Str63[64];
typedef unsigned char                   Str32[33];
typedef unsigned char                   Str31[32];
typedef unsigned char                   Str27[28];
typedef unsigned char                   Str15[16];
/*
    The type Str32 is used in many AppleTalk based data structures.
    It holds up to 32 one byte chars.  The problem is that with the
    length byte it is 33 bytes long.  This can cause weird alignment
    problems in structures.  To fix this the type "Str32Field" has
    been created.  It should only be used to hold 32 chars, but
    it is 34 bytes long so that there are no alignment problems.
*/
typedef unsigned char                   Str32Field[34];
/*
    QuickTime 3.0:
    The type StrFileName is used to make MacOS structs work 
    cross-platform.  For example FSSpec or SFReply previously
    contained a Str63 field.  They now contain a StrFileName
    field which is the same when targeting the MacOS but is
    a 256 char buffer for Win32 and unix, allowing them to
    contain long file names.
*/
typedef Str63                           StrFileName;
typedef unsigned char *                 StringPtr;
typedef StringPtr *                     StringHandle;
typedef const unsigned char *           ConstStringPtr;
typedef const unsigned char *           ConstStr255Param;
typedef const unsigned char *           ConstStr63Param;
typedef const unsigned char *           ConstStr32Param;
typedef const unsigned char *           ConstStr31Param;
typedef const unsigned char *           ConstStr27Param;
typedef const unsigned char *           ConstStr15Param;
typedef ConstStr63Param                 ConstStrFileNameParam;
#ifdef __cplusplus
inline unsigned char StrLength(ConstStr255Param string) { return (*string); }
#else
#define StrLength(string) (*(const unsigned char *)(string))
#endif  /* defined(__cplusplus) */

#if OLDROUTINENAMES
#define Length(string) StrLength(string)
#endif  /* OLDROUTINENAMES */

/********************************************************************************

    Process Manager type ProcessSerialNumber (previously in Processes.h)

*********************************************************************************/
/* type for unique process identifier */
struct ProcessSerialNumber {
  UInt32              highLongOfPSN;
  UInt32              lowLongOfPSN;
};
typedef struct ProcessSerialNumber      ProcessSerialNumber;
typedef ProcessSerialNumber *           ProcessSerialNumberPtr;
/********************************************************************************

    Quickdraw Types
    
        Point               2D Quickdraw coordinate, range: -32K to +32K
        Rect                Rectangular Quickdraw area
        Style               Quickdraw font rendering styles
        StyleParameter      Style when used as a parameter (historical 68K convention)
        StyleField          Style when used as a field (historical 68K convention)
        CharParameter       Char when used as a parameter (historical 68K convention)
        
    Note:   The original Macintosh toolbox in 68K Pascal defined Style as a SET.  
            Both Style and CHAR occupy 8-bits in packed records or 16-bits when 
            used as fields in non-packed records or as parameters. 
        
*********************************************************************************/
struct Point {
  short               v;
  short               h;
};
typedef struct Point                    Point;
typedef Point *                         PointPtr;
struct Rect {
  short               top;
  short               left;
  short               bottom;
  short               right;
};
typedef struct Rect                     Rect;
typedef Rect *                          RectPtr;
struct FixedPoint {
  Fixed               x;
  Fixed               y;
};
typedef struct FixedPoint               FixedPoint;
struct FixedRect {
  Fixed               left;
  Fixed               top;
  Fixed               right;
  Fixed               bottom;
};
typedef struct FixedRect                FixedRect;

typedef short                           CharParameter;
enum {
  normal                        = 0,
  bold                          = 1,
  italic                        = 2,
  underline                     = 4,
  outline                       = 8,
  shadow                        = 0x10,
  condense                      = 0x20,
  extend                        = 0x40
};

typedef unsigned char                   Style;
typedef short                           StyleParameter;
typedef Style                           StyleField;


/********************************************************************************

    QuickTime TimeBase types (previously in Movies.h)
    
        TimeValue           Count of units
        TimeScale           Units per second
        CompTimeValue       64-bit count of units (always a struct) 
        TimeValue64         64-bit count of units (long long or struct) 
        TimeBase            An opaque reference to a time base
        TimeRecord          Package of TimeBase, duration, and scale
        
*********************************************************************************/
typedef SInt32                          TimeValue;
typedef SInt32                          TimeScale;
typedef wide                            CompTimeValue;
typedef SInt64                          TimeValue64;
typedef struct TimeBaseRecord*          TimeBase;
struct TimeRecord {
  CompTimeValue       value;                  /* units (duration or absolute) */
  TimeScale           scale;                  /* units per second */
  TimeBase            base;                   /* refernce to the time base */
};
typedef struct TimeRecord               TimeRecord;

/********************************************************************************

    THINK C base objects

        HandleObject        Root class for handle based THINK C++ objects
        PascalObject        Root class for pascal style objects in THINK C++ 

*********************************************************************************/
#if defined(__SC__) && !defined(__STDC__) && defined(__cplusplus)
        class __machdl HandleObject {};
        #if TARGET_CPU_68K
            class __pasobj PascalObject {};
        #endif
#endif


/********************************************************************************

    MacOS versioning structures
    
        VersRec                 Contents of a 'vers' resource
        VersRecPtr              Pointer to a VersRecPtr
        VersRecHndl             Resource Handle containing a VersRec
        NumVersion              Packed BCD version representation (e.g. "4.2.1a3" is 0x04214003)
        UniversalProcPtr        Pointer to classic 68K code or a RoutineDescriptor
        
        ProcHandle              Pointer to a ProcPtr
        UniversalProcHandle     Pointer to a UniversalProcPtr
        
*********************************************************************************/
#if TARGET_RT_BIG_ENDIAN
struct NumVersion {
                                              /* Numeric version part of 'vers' resource */
  UInt8               majorRev;               /*1st part of version number in BCD*/
  UInt8               minorAndBugRev;         /*2nd & 3rd part of version number share a byte*/
  UInt8               stage;                  /*stage code: dev, alpha, beta, final*/
  UInt8               nonRelRev;              /*revision level of non-released version*/
};
typedef struct NumVersion               NumVersion;
#else
struct NumVersion {
                                              /* Numeric version part of 'vers' resource accessable in little endian format */
  UInt8               nonRelRev;              /*revision level of non-released version*/
  UInt8               stage;                  /*stage code: dev, alpha, beta, final*/
  UInt8               minorAndBugRev;         /*2nd & 3rd part of version number share a byte*/
  UInt8               majorRev;               /*1st part of version number in BCD*/
};
typedef struct NumVersion               NumVersion;
#endif  /* TARGET_RT_BIG_ENDIAN */

enum {
                                        /* Version Release Stage Codes */
  developStage                  = 0x20,
  alphaStage                    = 0x40,
  betaStage                     = 0x60,
  finalStage                    = 0x80
};

union NumVersionVariant {
                                              /* NumVersionVariant is a wrapper so NumVersion can be accessed as a 32-bit value */
  NumVersion          parts;
  UInt32              whole;
};
typedef union NumVersionVariant         NumVersionVariant;
typedef NumVersionVariant *             NumVersionVariantPtr;
typedef NumVersionVariantPtr *          NumVersionVariantHandle;
struct VersRec {
                                              /* 'vers' resource format */
  NumVersion          numericVersion;         /*encoded version number*/
  short               countryCode;            /*country code from intl utilities*/
  Str255              shortVersion;           /*version number string - worst case*/
  Str255              reserved;               /*longMessage string packed after shortVersion*/
};
typedef struct VersRec                  VersRec;
typedef VersRec *                       VersRecPtr;
typedef VersRecPtr *                    VersRecHndl;
/*********************************************************************************

    Old names for types
        
*********************************************************************************/
typedef UInt8                           Byte;
typedef SInt8                           SignedByte;
typedef wide *                          WidePtr;
typedef UnsignedWide *                  UnsignedWidePtr;
typedef Float80                         extended80;
typedef Float96                         extended96;
typedef SInt8                           VHSelect;
/*********************************************************************************

    Debugger functions
    
*********************************************************************************/
/*
 *  Debugger()
 *  
 *  Availability:
 *    Mac OS X:         in version 10.0 and later in CoreServices.framework
 *    CarbonLib:        in CarbonLib 1.0 and later
 *    Non-Carbon CFM:   in InterfaceLib 7.1 and later
 */
extern void 
Debugger(void)                                                __OSX_AVAILABLE_BUT_DEPRECATED(__MAC_10_0, __MAC_10_8, __IPHONE_NA, __IPHONE_NA);


/*
 *  DebugStr()
 *  
 *  Availability:
 *    Mac OS X:         in version 10.0 and later in CoreServices.framework
 *    CarbonLib:        in CarbonLib 1.0 and later
 *    Non-Carbon CFM:   in InterfaceLib 7.1 and later
 */
extern void 
DebugStr(ConstStr255Param debuggerMsg)                        __OSX_AVAILABLE_BUT_DEPRECATED(__MAC_10_0, __MAC_10_8, __IPHONE_NA, __IPHONE_NA);


/*
 *  debugstr()
 *  
 *  Availability:
 *    Mac OS X:         not available
 *    CarbonLib:        not available
 *    Non-Carbon CFM:   in InterfaceLib 7.1 and later
 */


#if TARGET_CPU_PPC
/* Only for Mac OS native drivers */
/*
 *  SysDebug()
 *  
 *  Availability:
 *    Mac OS X:         not available
 *    CarbonLib:        not available
 *    Non-Carbon CFM:   in DriverServicesLib 1.0 and later
 */


/*
 *  SysDebugStr()
 *  
 *  Availability:
 *    Mac OS X:         not available
 *    CarbonLib:        not available
 *    Non-Carbon CFM:   in DriverServicesLib 1.0 and later
 */


#endif  /* TARGET_CPU_PPC */

/* SADE break points */
/*
 *  SysBreak()
 *  
 *  Availability:
 *    Mac OS X:         in version 10.0 and later in CoreServices.framework
 *    CarbonLib:        in CarbonLib 1.0 and later
 *    Non-Carbon CFM:   in InterfaceLib 7.1 and later
 */
extern void 
SysBreak(void)                                                __OSX_AVAILABLE_BUT_DEPRECATED(__MAC_10_0, __MAC_10_8, __IPHONE_NA, __IPHONE_NA);


/*
 *  SysBreakStr()
 *  
 *  Availability:
 *    Mac OS X:         in version 10.0 and later in CoreServices.framework
 *    CarbonLib:        in CarbonLib 1.0 and later
 *    Non-Carbon CFM:   in InterfaceLib 7.1 and later
 */
extern void 
SysBreakStr(ConstStr255Param debuggerMsg)                     __OSX_AVAILABLE_BUT_DEPRECATED(__MAC_10_0, __MAC_10_8, __IPHONE_NA, __IPHONE_NA);


/*
 *  SysBreakFunc()
 *  
 *  Availability:
 *    Mac OS X:         in version 10.0 and later in CoreServices.framework
 *    CarbonLib:        in CarbonLib 1.0 and later
 *    Non-Carbon CFM:   in InterfaceLib 7.1 and later
 */
extern void 
SysBreakFunc(ConstStr255Param debuggerMsg)                    __OSX_AVAILABLE_BUT_DEPRECATED(__MAC_10_0, __MAC_10_8, __IPHONE_NA, __IPHONE_NA);


/* old names for Debugger and DebugStr */
#if OLDROUTINENAMES && TARGET_CPU_68K
    #define Debugger68k()   Debugger()
    #define DebugStr68k(s)  DebugStr(s)
#endif


#pragma pack(pop)

#ifdef __cplusplus
}
#endif

#endif /* __MACTYPES__ */


```



### CFNumber.h

```c
/*	CFNumber.h
	Copyright (c) 1999-2018, Apple Inc. and the Swift project authors
 
	Portions Copyright (c) 2014-2018, Apple Inc. and the Swift project authors
	Licensed under Apache License v2.0 with Runtime Library Exception
	See http://swift.org/LICENSE.txt for license information
	See http://swift.org/CONTRIBUTORS.txt for the list of Swift project authors
*/

#if !defined(__COREFOUNDATION_CFNUMBER__)
#define __COREFOUNDATION_CFNUMBER__ 1

#include <CoreFoundation/CFBase.h>

CF_IMPLICIT_BRIDGING_ENABLED
CF_EXTERN_C_BEGIN

typedef const struct CF_BRIDGED_TYPE(NSNumber) __CFBoolean * CFBooleanRef;

CF_EXPORT
const CFBooleanRef kCFBooleanTrue;
CF_EXPORT
const CFBooleanRef kCFBooleanFalse;

CF_EXPORT
CFTypeID CFBooleanGetTypeID(void);

CF_EXPORT
Boolean CFBooleanGetValue(CFBooleanRef boolean);

typedef CF_ENUM(CFIndex, CFNumberType) {
    /* Fixed-width types */
    kCFNumberSInt8Type = 1,
    kCFNumberSInt16Type = 2,
    kCFNumberSInt32Type = 3,
    kCFNumberSInt64Type = 4,
    kCFNumberFloat32Type = 5,
    kCFNumberFloat64Type = 6,	/* 64-bit IEEE 754 */
    /* Basic C types */
    kCFNumberCharType = 7,
    kCFNumberShortType = 8,
    kCFNumberIntType = 9,
    kCFNumberLongType = 10,
    kCFNumberLongLongType = 11,
    kCFNumberFloatType = 12,
    kCFNumberDoubleType = 13,
    /* Other */
    kCFNumberCFIndexType = 14,
    kCFNumberNSIntegerType API_AVAILABLE(macos(10.5), ios(2.0), watchos(2.0), tvos(9.0)) = 15,
    kCFNumberCGFloatType API_AVAILABLE(macos(10.5), ios(2.0), watchos(2.0), tvos(9.0)) = 16,
    kCFNumberMaxType = 16
};

typedef const struct CF_BRIDGED_TYPE(NSNumber) __CFNumber * CFNumberRef;

CF_EXPORT
const CFNumberRef kCFNumberPositiveInfinity;
CF_EXPORT
const CFNumberRef kCFNumberNegativeInfinity;
CF_EXPORT
const CFNumberRef kCFNumberNaN;

CF_EXPORT
CFTypeID CFNumberGetTypeID(void);

/*
	Creates a CFNumber with the given value. The type of number pointed
	to by the valuePtr is specified by type. If type is a floating point
	type and the value represents one of the infinities or NaN, the
	well-defined CFNumber for that value is returned. If either of
	valuePtr or type is an invalid value, the result is undefined.
*/
CF_EXPORT
CFNumberRef CFNumberCreate(CFAllocatorRef allocator, CFNumberType theType, const void *valuePtr);

/*
	Returns the storage format of the CFNumber's value.  Note that
	this is not necessarily the type provided in CFNumberCreate().
*/
CF_EXPORT
CFNumberType CFNumberGetType(CFNumberRef number);

/*
	Returns the size in bytes of the type of the number.
*/
CF_EXPORT
CFIndex CFNumberGetByteSize(CFNumberRef number);

/*
	Returns true if the type of the CFNumber's value is one of
	the defined floating point types.
*/
CF_EXPORT
Boolean CFNumberIsFloatType(CFNumberRef number);

/*
	Copies the CFNumber's value into the space pointed to by
	valuePtr, as the specified type. If conversion needs to take
	place, the conversion rules follow human expectation and not
	C's promotion and truncation rules. If the conversion is
	lossy, or the value is out of range, false is returned. Best
	attempt at conversion will still be in *valuePtr.
*/
CF_EXPORT
Boolean CFNumberGetValue(CFNumberRef number, CFNumberType theType, void *valuePtr);

/*
	Compares the two CFNumber instances. If conversion of the
	types of the values is needed, the conversion and comparison
	follow human expectations and not C's promotion and comparison
	rules. Negative zero compares less than positive zero.
	Positive infinity compares greater than everything except
	itself, to which it compares equal. Negative infinity compares
	less than everything except itself, to which it compares equal.
	Unlike standard practice, if both numbers are NaN, then they
	compare equal; if only one of the numbers is NaN, then the NaN
	compares greater than the other number if it is negative, and
	smaller than the other number if it is positive. (Note that in
	CFEqual() with two CFNumbers, if either or both of the numbers
	is NaN, true is returned.)
*/
CF_EXPORT
CFComparisonResult CFNumberCompare(CFNumberRef number, CFNumberRef otherNumber, void *context);

CF_EXTERN_C_END
CF_IMPLICIT_BRIDGING_DISABLED

#endif /* ! __COREFOUNDATION_CFNUMBER__ */


```



### [Type Encodings](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtTypeEncodings.html)

| Code               | Meaning                                                      |
| ------------------ | ------------------------------------------------------------ |
| `c`                | A `char`                                                     |
| `i`                | An `int`                                                     |
| `s`                | A `short`                                                    |
| `l`                | A `long``l` is treated as a 32-bit quantity on 64-bit programs. |
| `q`                | A `long long`                                                |
| `C`                | An `unsigned char`                                           |
| `I`                | An `unsigned int`                                            |
| `S`                | An `unsigned short`                                          |
| `L`                | An `unsigned long`                                           |
| `Q`                | An `unsigned long long`                                      |
| `f`                | A `float`                                                    |
| `d`                | A `double`                                                   |
| `B`                | A C++ `bool` or a C99 `_Bool`                                |
| `v`                | A `void`                                                     |
| `*`                | A character string (`char *`)                                |
| `@`                | An object (whether statically typed or typed `id`)           |
| `#`                | A class object (`Class`)                                     |
| `:`                | A method selector (`SEL`)                                    |
| [*array type*]     | An array                                                     |
| {*name=type...*}   | A structure                                                  |
| (*name*=*type...*) | A union                                                      |
| `b`num             | A bit field of *num* bits                                    |
| `^`type            | A pointer to *type*                                          |
| `?`                | An unknown type (among other things, this code is used for function pointers) |

