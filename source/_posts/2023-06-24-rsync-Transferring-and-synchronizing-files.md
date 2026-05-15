---
layout: post
title: rsync - Transferring and synchronizing files 
categories: [Linux]
tags: [download]
author: wpsze
index_img: 
banner_img: 
---

# rsync

rsync (remote sync) 是Unix下的一款套用軟件，它能同步更新兩處電腦的檔案與目錄，並適當利用差分編碼以減少數據傳輸量。rsync中的一項同類軟件不常見的重要特性是每個目標的鏡像只需傳送一次。rsync可以拷貝／顯示目錄內容，以及拷貝檔案，並可選壓縮以及遞歸拷貝。

在常駐模式（daemon mode）下，rsync預設監聽 `TCP埠 873`，以原生rsync傳輸協定或者透過遠端shell如RSH或者SSH提供檔案。SSH模式下，rsync用戶端執行程式必須同時在本機和遠端機器上安裝。

```sh
rsync 的基本語法結構如下：

rsync 參數 來源檔案 目的檔案
以下是最常見的幾個參數：

-v：verbose 模式，輸出比較詳細的訊息。
-r：遞迴（recursive）備份所有子目錄下的目錄與檔案。
-a：封裝備份模式，相當於 -rlptgoD，遞迴備份所有子目錄下的目錄與檔案，保留連結檔、檔案的擁有者、群組、權限以及時間戳記。
-z：啟用壓縮。
-h：將數字以比較容易閱讀的格式輸出。
```

1. 如果要讓 rsync 在傳輸檔案時可以即時顯示進度，可以加上 --progress 參數。
2. -P參數是--progress和--partial這兩個參數的結合，使用此選項時，rsync 在傳輸過程中**顯示進度條**並**保留部分傳輸的檔案**。透過緩慢或不穩定的網路連線傳輸大檔案時非常有用。

```console
rsync -aP folder username@target-ip:/home/user/xxx/

rsync -aP --exclude='*.txt' --exclude 'dir1/*' source/ destination
rsync -aP --exclude={'*.txt', 'file1.txt','dir1/*'} source/ destination
rsync -aP --exclude-from='exclude-file.txt' source/ destination/
rsync -av --include="*.txt" --exclude='*' source/ destination
```

and,

{% fold info @script %}
```sh
#!/bin/bash
#Here's how this script works:

# Replace the REMOTE_HOST, REMOTE_PATH, and LOCAL_PATH variables with your actual remote host details, remote file path, and local directory path, respectively.
# The LOCK_FILE variable specifies the path to the lock file, which will be used to prevent multiple instances of the script from running simultaneously.
# The is_locked function checks if the lock file exists.
# The create_lock function creates the lock file.
# The remove_lock function removes the lock file.
# The perform_rsync function performs the actual rsync -z command to copy the files from the remote machine to the local machine.
# The script checks if the lock file exists. If it does, it means the previous rsync is still running, so it exits without starting a new rsync.
# If the lock file doesn't exist, the script creates the lock file, performs the rsync, and then removes the lock file.

INIT_DIR=$1

REMOTE_HOST="user@ip"
PORT="1234567"
REMOTE_PATH="/home/user/${INIT_DIR}/file.*" 
LOCAL_PATH="/home/user/${INIT_DIR}/" 
LOCK_FILE="${LOCAL_PATH}/rsync.lock" 
LOG_FILE="${LOCAL_PATH}/rsync.log"
# Function to check if the lock file exists
is_locked() {
    [ -f "$LOCK_FILE" ]
}

# Create the lock file
create_lock() {
    touch "$LOCK_FILE"
}

# Remove the lock file
remove_lock() {
    rm "$LOCK_FILE"
}

# Function to perform rsync
perform_rsync() {
    rsync -azvhP -e 'ssh -p ' "$PORT" "$REMOTE_HOST:$REMOTE_PATH" "$LOCAL_PATH" --log-file=${LOG_FILE}
}

# Check if the lock file exists
if is_locked; then
    echo "Previous rsync is still running. Exiting."
    exit 1
fi

# Create the lock file
create_lock

# Perform rsync
perform_rsync

# Remove the lock file
remove_lock
```
{% endfold %}

This can be submitted as cron job and automatically downloads new files generated (e.g. every hour). 
It checks whether the previous rsync job is done before rsync-ing again, otherwise waits for another time interval defined in the crontab (it generates a 'lock' file when starting a rsync and deleting it afterwards. If the 'lock' file is present it will not run rsync)


# include/exclude

`rsync` supports the `--include-from` option, which allows you to specify include patterns in a file instead of listing multiple `--include` options on the command line. This is useful for managing complex include patterns or when you have many file types to include.

## Syntax

```bash
rsync [options] --include-from='file' source destination
```

## Key Points

- The `--include-from='file'` option reads include patterns from a specified file.
- Each line in the file is treated as an include pattern (e.g., `*.txt`, `*.pdf`).
- Combine with `--exclude='*'` to include only the patterns listed in the file and exclude everything else.
- The file can also contain `--exclude` patterns if needed (prefix with `-` for exclude).
- Patterns in the file follow the same syntax as `--include` and `--exclude`.

## How to Use `--include-from`

1. **Create a file** (e.g., `include.txt`) with include patterns, one per line.
2. **Use `--include-from`** to reference the file in your `rsync` command.
3. **Test with `--dry-run`** to verify the patterns.
4. **Run the command** to synchronize files.

## Example

### Step 1: Create the include file

Create a file named `include.txt` with the following content:

```
*.txt
*.pdf
*.jpg
```

### Step 2: Run rsync

```bash
rsync -av --include-from='include.txt' --exclude='*' /source/ /destination/
```

- This syncs only `.txt`, `.pdf`, and `.jpg` files from `/source/` to `/destination/`, excluding all other files.

## Advanced Example

### Include file with directories and exclusions

`include.txt`:

```
+ */                 # Include all directories for traversal
+ *.txt             # Include .txt files
+ *.pdf             # Include .pdf files
- *.bak             # Exclude .bak files
```

### Command

```bash
rsync -av --include-from='include.txt' --exclude='*' /source/ /destination/
```

- Includes `.txt` and `.pdf` files recursively, excludes `.bak` files, and ignores all other files.

## Notes

- **Pattern syntax in file**:
  - Use `+ ` (plus and space) for include patterns (optional, but clearer).
  - Use `- ` (minus and space) for exclude patterns.
  - Blank lines and lines starting with `#` are ignored (useful for comments).
- **Order matters**: Patterns are processed in the order they appear in the file.
- **Relative paths**: The `--include-from` file path is relative to the current working directory.
- **Combine with other options**: You can still use `--include` or `--exclude` on the command line, but `--include-from` patterns are processed afterward.
- **Debugging**: Use `--dry-run` and `-v` to verify which files are included/excluded.

## Tips
- Use `--include-from` for large or reusable pattern lists to keep commands clean.
- Store the include file in a version-controlled location if used frequently.
- Test complex patterns with `rsync -av --dry-run --include-from='file'`.

# 跳板連線

要實作 myPC ➔ 代理伺服器 A（Port: 231456）➔ 目標伺服器（Port: 12345） 的 SSH 隧道設定，你需要修改 `~/.ssh/config` 檔案。
由於單個 TCP/UDP 連接埠的最大數值是 65535，你提到的 231456 已超出範圍。以下設定範例將假設代理伺服器 A 的實際埠號為 23145（或請改為您實際的 5 位數以內埠號）。
請將以下內容貼上至你的 `~/.ssh/config`：

## 1. 先定義代理伺服器 A 的連線資訊

```
Host proxy-A
    HostName 代理伺服器A的IP或網址
    User 你的A伺服器帳號
    Port 23145
```

## 2. 定義目標伺服器，並透過 proxy-A 進行跳板連線

```
Host target-server
    HostName 目標伺服器的IP或網址
    User 你的目標伺服器帳號
    Port 12345
    ProxyCommand ssh -W %h:%p proxy-A
```

### Rsync 執行指令
設定完成後，你的 rsync 指令會變得非常簡潔，完全不需要在指令中重複輸入埠號和代理設定：

```
rsync -av /local/path/ target-server:/remote/path/
```

### 設定要點說明

* `ProxyCommand ssh -W %h:%p proxy-A`：這是最現代且安全的 SSH 跳板方法。它會先透過 proxy-A 設定建立安全通道，再將流量轉發至 target-server 的 12345 埠。
* 自動繼承：rsync 會自動讀取 target-server 區塊內的 Port 12345 以及跳板設定。


