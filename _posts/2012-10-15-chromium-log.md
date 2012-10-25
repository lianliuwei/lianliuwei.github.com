---
layout: post
title: "chromium log"
description: ""
category: code
tags: [chromium_base, Google, C++]
---
{% include JB/setup %}

chromium log介绍和使用
==

chromium log是chromium_base基础模块中的一部分，用于logging，logging当然是为了解程序的运行状态，特别是失败，或者是关键操作的情况。总不能每次都用调试器去看变量和单步运行。logging有非常多的扩展需求，有的要求支持并发，有的要求自动上传，有的要求支持性能分析。这些chromium log都不支持：），它适用于基本的程序出错记录和关键流程记录。当然chromium有其他满足这些要求的工具库。

 `base/logging.h` `base/vlog.h` 是相关的文件，开头注释有详细的使用介绍。本文可以看成是它的翻译和补充。

### 日志级别 ###
日志级别为两种，verbose logging级，和logging级，verbose logging级用正整数表示，如`VLOG(1)，VLOG(2)`等，logging级有`
INFO WARNING ERROR ERROR_REPORT FATAL`。程序运行时候有个当前LOG级别，如果将VLOG的级别看成对应的负整数，而logging级对应0到4。如果某个日志输出比当前LOG级别低，响应的LOG就会被忽略，反之则会输出相关的日志。可以使用命令行设置全局，或者针对文件的当前LOG级别。

### 普通LOG ###
LOG()宏是用来参加的打logging宏，如
`LOG(INFO) << "Found " << num_cookies << " cookies";` << 输出符号，支持常用的和chromium内部常用的数据类型。
### 断言 ###
`CHECK()`这个是最常用的宏

### 条件断言 ###
宏`LOG_IF(severity, condition)`可以实现特定条件下的LOG，如 `LOG_IF(INFO, num_cookies > 10) << "Got lots of cookies";` 

### Debug版本LOG ###
比如`CHECK()`就有一个对应的`DCHECK()`, 示例


    DLOG(INFO) << "Found cookies";
    DLOG_IF(INFO, num_cookies > 10) << "Got lots of cookies";


### 系统调用出错内容获得 ###

使用相关的P开头的验证宏会去查找最后一次系统调用出错信息，并获得相关的readable信息，一并打印出来，省去了自己写这些繁琐的操作。

示例：
`  PCHECK(chdir(dir) == 0); `


### 调用堆栈记录 ###

整合了系统功能，提供log触发的时候的调用堆栈，对于查找错误调用很有帮助， 示例堆栈记录在下，Windows下有pdb还能找到打印出函数名。调用点是在~LogMessage的上一层。

    [58000:38236:0821/155941:690170559:FATAL:histogram.cc(854)] Check failed: ValidateCustomRanges(custom_ranges). 
    Backtrace:
    	base::debug::StackTrace::StackTrace [0x60817711+33] (d:\workspace\chromium_base_new\base\debug\stack_trace_win.cc:149)
    	logging::LogMessage::~LogMessage [0x606B86EE+94] (d:\workspace\chromium_base_new\base\logging.cc:563)
    	base::CustomHistogram::FactoryGet [0x607C4E67+231] (d:\workspace\chromium_base_new\base\metrics\histogram.cc:856)
    	base::HistogramDeathTest_BadRangesTest_Test::TestBody [0x017E7C85+3029] (d:\workspace\chromium_base_new\base\metrics\histogram_unittest.cc:355)
    	testing::internal::HandleExceptionsInMethodIfSupported<testing::Test,void> [0x0192D2FC+316] (d:\workspace\chromium_base_new\testing\gtest\src\gtest.cc:2126)
    	testing::Test::Run [0x01915CEE+174] (d:\workspace\chromium_base_new\testing\gtest\src\gtest.cc:2143)
    	testing::TestInfo::Run [0x0191681D+221] (d:\workspace\chromium_base_new\testing\gtest\src\gtest.cc:2323)
    	testing::TestCase::Run [0x01916FFF+239] (d:\workspace\chromium_base_new\testing\gtest\src\gtest.cc:2427)
    	testing::internal::UnitTestImpl::RunAllTests [0x0191DDCD+701] (d:\workspace\chromium_base_new\testing\gtest\src\gtest.cc:4250)
    

默认在FATAL级别下会打印调用堆栈。

### 全局log设置 ###
可以通过v模式设置全局的logging级别，比如 `--v=1` 就是设置最小为WARNING级别。

### 分文件log级别选择 ###

这个是个杀手级别的功能，与其事后处理log文本，不如在源头过滤掉，这样也可以节省点logging造成的系统性能损失。在调试的时候可以轻松根据需求，对于特定文件设置不同的调试级别。相关命令行为选项为`--vmodule=my_module=2,foo*=3`，这个用来设置my_module.*" and "foo*.*的文件的log级别。或者使用`"--vmodule=*/foo/bar/*=2`，用于指定foo/bar下的文件。

### 最小示例 ###
   
    #include "base/logging.h"
    #include "base/command_line.h"
    
    //still leak some memory because the global obj VlogInfo in logging. not method
    //to release
    int main(int argc, char** argv) {
      using namespace logging;
    
      CommandLine::Init(argc, argv);
    
      InitLogging(L"debug2.log", LOG_TO_BOTH_FILE_AND_SYSTEM_DEBUG_LOG,
                  DONT_LOCK_LOG_FILE, DELETE_OLD_LOG_FILE,
                  DISABLE_DCHECK_FOR_NON_OFFICIAL_RELEASE_BUILDS);
     
       return 0;
    }

其中InitLogging一定要在第一次LOG前调用。(存疑，LOG功能好像不用出示话就能使用)


tips
--

1. main函数中初始化log，
2. 使用log功能造成内存泄露
3. 使用命令行设置过滤文件名，使用小写，带cc后缀，去除-inl后缀。
4. 用`NOTIMPLEMENTED()`来表示没有实现的功能
5. 用`NOTREACHED()`来表示不应该到达的路径
6. 用0， -1，到-4代表相关的logging级别，可以用于vmodule中提高log级别
7. 默认优先级是0
8. 路径和文件名不要使用空格，特别是代码。

常见错误
--

1. 在DCHECK()宏中写执行语句，造成release版本下代码被注释掉。

    千万要记住DCHECK()是个宏，有可能被定义为空。执行语句写里面可能会被注释掉。这样程序就隐藏了一个很大的bug，而且只会在release重现。如果非要这么写，记得使用相关非DEBUG版本宏
2. LOG只支持ANSI字符，所以不要试图向里面写入其他编码的字符。

