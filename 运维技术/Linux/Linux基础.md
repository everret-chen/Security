# Linux基础

## shell重定向
```bash  ```
一个进程启动时默认打开三个“流”：

文件描述符	  名称	   符号	   默认指向	  用途
   0	    标准输入	   stdin	 键盘	    读取数据
   1	    标准输出	   stdout	 屏幕	    输出正常结果
   2	    标准错误	   stderr	 屏幕	    输出错误/诊断信息

### 常见重定向写法
语法	效果
> file	        将 stdout 写入 file（覆盖）；等同于 1> file
>> file	        将 stdout 追加到 file
2> file  	      将 stderr 写入 file（覆盖）
2>> file	      将 stderr 追加到 file
&> file 或 >&	  将 stdout 和 stderr 都写入 file（覆盖）
command < file	将 file 作为 stdin
2>&1	          将 stderr 重定向到当前 stdout 指向的位置（合并）
1>&2	          将 stdout 重定向到 stderr 的位置

### /dev/null 的妙用
场景	              命令示例
丢弃所有输出（stdout+stderr）	cmd &> /dev/null
只丢弃错误，保留正常输出	cmd 2> /dev/null
只丢弃正常输出，仅看错误	cmd > /dev/null
测试命令是否成功（不显示输出）	cmd >/dev/null 2>&1 && echo ok
注意 &> 是 Bash 的简洁写法，对于 POSIX sh 可移植性要求高的场景，常用 >file 2>&1。
