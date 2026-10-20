---
layout: post
title: Linux | 如何分析可執行程序？例如使用 ldd 或其他工具？
categories: [Linux]
tags: [conda, Micromamba, ldd, gdb]
author: wpsze
date: 2026-10-20 06:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://thumb.wikimedia.org/wikipedia/commons/thumb/6/69/GNU_gdb_12.1_screenshot.png/500px-GNU_gdb_12.1_screenshot.png?utm_source=zh.wikipedia.org&utm_campaign=parser&utm_content=thumbnail
banner_img: https://thumb.wikimedia.org/wikipedia/commons/thumb/6/69/GNU_gdb_12.1_screenshot.png/500px-GNU_gdb_12.1_screenshot.png?utm_source=zh.wikipedia.org&utm_campaign=parser&utm_content=thumbnail
---

From Gemini

---

# Executable Binary Analysis

分析可執行程序（Executable Binary Analysis）通常分為**靜態分析**（不運行程序）和**動態分析**（運行程序並觀察行為）。以下是常見的工具與分析流程：

---

## 一、 靜態分析工具（Static Analysis）

靜態分析在不安裝或不執行程序的情況下，檢查其結構、依賴項與符號資訊。

### 1. `ldd` — 查看動態連結庫依賴

用於查看程序運行時需要加載的共享庫（Shared Libraries, `.so`）。

```bash
ldd /path/to/binary

```

* **常見用途**：檢查是否缺少依賴庫（如 `not found` 錯誤）。
* **注意事項**：不要對**來源不明的未知程序**直接運行 `ldd`，因為在某些系統上 `ldd` 可能會嘗試加載並執行部分代碼。

### 2. `file` — 查看文件基本類型與架構

確定程序的檔案類型（ELF、PE、Mach-O）、架構（x86_64、AArch64）以及編譯屬性（如是否為 `statically linked` 或 `dynamically linked`）。

```bash
file /path/to/binary

```

### 3. `readelf` — 查看 ELF 文件細節 (Linux)

針對 Linux 下的 ELF 格式程序，提供比 `ldd` 更深入且安全的靜態分析：

```bash
# 查看動態依賴庫（安全替代 ldd）
readelf -d /path/to/binary | grep NEEDED

# 查看程序頭部資訊（包含 Entry Point 進入點）
readelf -h /path/to/binary

# 查看段落結構 (Sections)
readelf -S /path/to/binary

```

### 4. `nm` — 查看符號表（Symbols）

列出程序中定義與引用的函數和全域變數（若未被 `strip` 剝離）：

```bash
nm -D /path/to/binary    # 查看動態符號
nm -C /path/to/binary    # 解析 C++ 名稱修飾 (Demangle)

```

### 5. `objdump` — 反匯編與節區查看

將機器碼還原為組合語言（Disassembly）：

```bash
# 反匯編 .text 節（主要代碼段）
objdump -d -M intel /path/to/binary | less

# 查看包含來源代碼行的反匯編（若包含 Debug symbol）
objdump -S /path/to/binary

```

---

## 二、 動態分析工具（Dynamic Analysis）

動態分析通過追蹤程序執行過程中的系統調用、函數調用與記憶體狀態來分析行為。

### 1. `strace` — 追蹤系統調用 (System Calls)

記錄程序向 Linux 核心發出的所有請求（如檔案讀寫 `open`/`read`/`write`、網路通訊 `socket` 等）。

```bash
strace -f -o trace.log ./binary

```

* **常用參數**：`-f` 追蹤子進程，`-e` 過濾特定調用（例如 `-e trace=file,network`）。

### 2. `ltrace` — 追蹤動態庫函數調用 (Library Calls)

記錄程序調用了哪些 C 標準庫或其他共享庫函數（如 `malloc`, `printf`, `strcmp`）。

```bash
ltrace ./binary

```

### 3. `gdb` — GNU 調試器 (Interactive Debugging)

進行單步調試、設置中斷點（Breakpoints）、檢查寄存器與記憶體內容：

```bash
gdb ./binary
(gdb) break main      # 在 main 函數設中斷點
(gdb) run             # 開始執行
(gdb) info registers  # 查看寄存器內容
(gdb) x/10xw $rsp     # 檢查堆疊記憶體

```

---

## 三、 高級逆向工程工具（GUI / 全功能分析平台）

對於複雜、未開源或高度混淆的二進制文件，可以使用專門的逆向工程工具：

* **Ghidra**：NSA 開源的逆向工程套件，內置強大的 C 語言反編譯器（Decompiler）。
* **IDA Pro / IDA Free**：業界標準的反編譯與動態調試工具。
* **r2 (Redit2)** / **Cutter**：命令列與 GUI 結合的開源逆向框架。

---

## 四、 常用分析流程總結

1. **初步偵查**：用 `file` 確定架構，用 `ldd` 或 `readelf -d` 查看依賴。
2. **文本提取**：用 `strings /path/to/binary` 搜尋程序中硬編碼的敏感字串（如網址、路徑、提示訊息）。
3. **行為追蹤**：用 `strace` 查看程序在操作哪些檔案與網絡鏈接。
4. **深入逆向**：使用 `Ghidra` 或 `gdb` 分析核心邏輯。


在 Linux / Unix 系統中，**`not stripped`** 表示該可執行文件或動態連結庫（`.so`）中**保留了完整的符號表（Symbol Table）與除錯資訊（Debug Information）**。

當你使用 `file` 命令檢查二進位檔案時，常會看到類似的輸出：

```bash
$ file my_program
my_program: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, ... , not stripped

```

---

# **Not Stripped**（未剝離）

## 一、 什麼是符號（Symbols）？

在編譯 C/C++ 等程式碼時，符號表上記錄了：

* **函數名稱**（如 `main`, `calculate_sum()`）
* **全域變數名稱**（如 `int global_counter`）
* **原始碼檔名與行號**（若編譯時加上 `-g` 開啟 Debug mode）

---

## 二、 `not stripped` vs `stripped` 的主要差異

| 特性 | **Not Stripped**（未剝離） | **Stripped**（已剝離） |
| --- | --- | --- |
| **符號資訊** | 完全保留（包含函數名、變數名） | 絕大部分名稱被移除或替換為記憶體位址 |
| **檔案大小** | 較大（因為包含額外的文字資訊） | 較小（通常可減少 10% ~ 50% 體積） |
| **可讀性 / 反彙編** | 非常高（反組譯工具可直接還原原函數名） | 較低（只能看到 `sub_401120` 等無意義名稱） |
| **除錯難易度** | 非常容易（可用 `gdb` 設定函數中斷點） | 困難（需要載入額外的 `.debug` 檔案） |
| **主要用途** | 開發階段、除錯、內部測試 | 正式發佈（Release Mode）、減少體積、保護程式邏輯 |

---

## 三、 如何檢視與操作符號？

### 1. 查看未剝離（not stripped）檔案的函數名稱

可以使用 `nm` 或 `readelf` 檢查：

```bash
nm my_program

```

> **輸出範例**（能直接看到 `my_function`）：
> `0000000000001149 T my_function`
> `0000000000001129 T main`

### 2. 將檔案變更為 `stripped`

Linux 提供 `strip` 命令，可直接移除二進位檔案中的符號資訊以縮減檔案大小：

```bash
strip my_program

```

執行後再用 `file my_program` 檢查，狀態就會變成 **`stripped`**。

---

## 四、 對分析與逆向工程的影響

* **開發與除錯**：`not stripped` 讓 `gdb` 等除錯工具能直接對應原始碼行號與函數名稱。
* **逆向分析**：若拿到的二進位檔案是 `not stripped`，使用 Ghidra、IDA Pro 等逆向工具時，函數名稱會直接顯示在反組譯碼中，大幅降低分析門檻。

使用 `strip` 命令可以非常方便地清除 ELF 可執行檔或共享庫（`.so`）中的除錯符號與非必要資訊，從而縮小檔案體積並提高逆向分析的門檻。

---

# 如何使用 strip 命令将 not stripped 的文件转换为 stripped

## 1. 基本語法與操作步驟

在使用 `strip` 前，建議先備份原始檔案或將處理後的檔案輸出至新路徑。

### **方法 A：直接修改原檔案（不可逆）**

```bash
strip ./my_program

```

* **驗證方式**：執行 `file ./my_program`，輸出中應包含 `stripped` 字樣。

### **方法 B：將處理後的結果輸出至新檔案（推薦）**

使用 `-o` 參數可以保留原始的 `not stripped` 檔案，方便日後除錯：

```bash
strip ./my_program -o ./my_program_stripped

```

* **驗證方式**：執行 `file ./my_program_stripped` 確認顯示為 `stripped`，並使用 `ls -lh my_program*` 比對兩者檔案大小差異。

---

## 2. 進階使用技巧（控制清除程度）

預設情況下，`strip` 會移除所有非必要的符號表（包括除錯資訊與本地符號）。你可以透過參數進行更精細的控制：

| 常用參數 | 功能說明 | 適用情境 |
| --- | --- | --- |
| **`strip --strip-debug <file>`** | **僅移除 Debug 符號**，保留一般函數與變數名稱 | 適用於動態庫（.so），確保外部鏈接與調用不受影響 |
| **`strip --strip-unneeded <file>`** | 移除所有重定位不需要的符號 | 最常見的清理方式 |
| **`strip -K <symbol_name> <file>`** | **保留特定符號**，其他全部移除 | 當某些函數（如 Plugin 接口）必須暴露時使用 |
| **`strip -g <file>`** / **`-S`** | 僅移除除錯（Debug）符號 | 類似 `--strip-debug` |

---

## 3. 注意事項

1. **共享庫（`.so` 檔案）**：
對於 `.so` 檔案，請勿使用過於激進的清除選項（例如清除全域導出符號），否則其他程式在執行時將無法順利載入或呼叫該庫中的函數。一般使用 `strip --strip-unneeded libtest.so` 即可。
2. **核心轉儲與除錯（Core Dump / GDB）**：
檔案一旦變成 `stripped`，程式崩潰時產生的 Core Dump 檔將無法直接顯示出有意義的函數名稱與行號。如果在正式環境需要除錯，通常會在編譯時透過 `objcopy` 將 Debug 符號獨立抽離為 `.debug` 檔案保存。


# 如何使用 objdump 或 gdb 來查看這類文件的符號表

要查看可執行檔或共享庫（`.so`）中的符號表，不論該檔案是 **`not stripped`**（保留完整符號表）還是 **`stripped`**（大部分符號已移除），都可以使用 `objdump` 或 `gdb` 來進行檢視。

---

## 一、 使用 `objdump` 查看符號表

`objdump` 是 ELF 靜態分析工具，能精準提取不同類型的符號表：

### 1. 查看一般符號表（Symbol Table, `.symtab`）

僅適用於 **`not stripped`** 的檔案，包含所有本地（local）與全域（global）函數及變數名稱：

```bash
objdump -t /path/to/binary

```

* **常見過濾技巧**（搜尋特定函數，如 `main`）：
```bash
objdump -t /path/to/binary | grep main

```

### 2. 查看動態符號表（Dynamic Symbol Table, `.dynsym`）

**不論檔案是否被 strip**，只要是動態連結（Dynamically Linked）的程式，匯出/匯入的共享庫介面符號都會保留在 `.dynsym` 中：

```bash
objdump -T /path/to/binary

```

* **輸出範例解析**：
* `*UND*`：代表未在此檔案內定義，需要執行時由動態連結器（如 `libc.so`）提供的外部函數（如 `printf`）。
* `g F .text`：代表全域（global）函數（Function）。



---

## 二、 使用 `gdb` 查看符號表

`gdb` 適合在互動式環境中查詢或測試符號的解析結果。

### 1. 載入檔案並檢查符號狀態

直接以 `gdb` 啟動檔案：

```bash
gdb ./binary

```

啟動後觀察提示訊息：

* 若顯示 `Reading symbols from ./binary...` 代表為 **`not stripped`**。
* 若顯示 `Reading symbols from ./binary...(no debugging symbols found)`，代表除錯符號已被剝離（`stripped`）。

### 2. 常見的 `gdb` 符號查詢指令

在 `gdb` 的命令列提示符 `(gdb)` 下使用：

* **搜尋所有函數符號**：
```text
(gdb) info functions

```


* *註：若已被 strip，只會列出動態符號表（如 `.plt` 區段的函數）。*


* **按關鍵字搜尋函數**：
```text
(gdb) info functions <keywords>

```


* 範例：`(gdb) info functions print`


* **查看全域/靜態變數符號**：
```text
(gdb) info variables

```


* **查看特定符號的記憶體位址或型態**：
```text
(gdb) ptype main
(gdb) print &main

```



---

## 三、 若檔案已被 `stripped`，該如何搭配獨立 Debug 檔？

若你手頭上有透過 `objcopy` 分離出來的 `.debug` 符號檔，可在 `gdb` 中直接載入並還原符號表：

```text
(gdb) file ./binary_stripped
(gdb) symbol-file ./binary.debug

```

載入成功後，再次執行 `info functions` 即可重新看到原本被隱藏的函數名稱與行號資訊。