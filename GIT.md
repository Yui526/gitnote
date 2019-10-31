# GIT

## 初次

### 安裝

Windows Explorer integration : Git Bash Here
Git LFS
Use Git from the Windows Command Prompt
Checkout Windows-style , commit Unix-style line endings(LF->CRLF)
Use MiniTTY

- 預設編輯器

  設定:git config --global core.editor notepad "路徑"
  		   若git不認得路徑,路徑要用" ' ' " 包住
  查詢:git config core.editor

### 設定

- name

	- git config --global user.name "xxx"

- email

	- git config --global user.email"xxx@xxx.com"

### 查詢設定

- git config user.name

## 基礎

### 初始化

- git init

### 更新索引

- git add xxx(檔案名稱)
git add . (all檔案)

### 建立版本

- git commit -am 'xxx'
- git commit -m 'xxx'

### 查詢

- 狀態

	- git status

	  on branch master--在哪個分支
	  Your branch is up to date with 'origin/master'--目前狀態

- log

	- git log
git log --oneline --graph(版本線圖)

- 分支異動所有紀錄

	- git reflog

## 分支

### WHAT

- 平行宇宙

	- 多人開發
	- 功能分支

### HOW

- 建立

	- git branch xxx(分支名稱)
git branch xxx [版本id] (從此版開始)
	- git checkout - b xxx

- 切換

	- git checkout xxx

- 刪除

	- git branch -d xxx

	  如果該分支有commit , 且未合併 , 則無法刪除
	  除非將參數改為-D

	- 條件式

	  git branch --merged | egrep -v "(^\*|master|develop)" | xargs git branch  -d
	  搜尋已合併過 , 且非master 或 develop 分支 , 並將其當參數傳給刪除

- 查詢

	- git branch

	  git branch -a(全部,包含遠端分支)
	  git branch -r(遠端追蹤分支)

### 合併

- 步驟

  假設要將分支develop併入master
  git checkout master (確認目前在master分支)
  git merge develop (合併)

	- git merge xxx(分支名稱)
	- git merge xxx --no-ff

- 衝突

	- 溝通
	- 處理

		- 編輯後 -> add -> commit

	- 放棄合併

		- git merge --abort

- 應用

	- git merge --no-ff --no-commit xxx

	  不自動commit , 自行再commit

	- git merge --squash xxx

	  壓縮合併 , 不保留分支合併的線圖

## 協同

### DOWNLOAD

- git clone https://github.com/abc/Test.git xxx(資料夾名稱)

  git clone --no-checkout ->一般預設checkout master
  git clone --bare -> 僅下載版控資料
  可再透過git clone c/project/xxx.git 下載全資料

- git pull

	- =git fetch + git merge

### UPLOAD

- 查詢設定

	- git remote -v

	  (fetch)抓檔對應位置
	  (push)推檔對應位置

- 新增設定

	- git remote add origin https://...

- 發布

	- git push --set-upstream origin master

	  --set-upstream 可簡寫為-u
	  其作用是預先設定 upstream 為origin master
	  之後推檔可直接輸入git push
	  而不用再輸入 git push origin master

### 應用

- 發布本地分支
取得遠端分支

  模擬A.B開發者 , 分支及指令後擁有的分支
  B會先取得遠端分支 , 並發布本地分支
  
  A(既有開發者)-demo1目前有以下分支:
  master
  develop
  remotes/origin/develop
  remotes/origin/master
  4.remotes/origin/new1
  5.new1
  
  B(新開發者)-demo2
  1.master
     remotes/origin/develop
     remotes/origin/master
  2.develop
  3.new1
  
  執行指令(數字對應上方步驟後擁有的分支)
  B-1.git clone xxx/demo1.git
  B-2.git checkout develop(自動建立關聯)
  B-3.git checkout -b new1
  發布 git push -u origin new1
  A-4:git fetch
  A-5:git checkout new1

	- 發布

		- git push -u origin new1

	- 取得

		- git fetch

- 刪除遠端分支/遠端追蹤分支

  沿用上述模擬情境 , A開發者取得new1分支 , 開發完後併入develop分支 , 並將new1分支刪除
  
  執行指令
  1.先合併分支 , 推上去
  A-1:git checkout develop
  A-2:git merge --no-ff new1
  A-3:git push
  B拉下來
  B-4:git checkout develop
  B-5:git pull
  
  2.刪除
  A-1本地分支:git branch -d new1
  A-2遠端分支:git push origin --delete new1
  遠端追蹤分支會一併刪除
  
  B-3:git fetch --prune(自動修剪遠端追蹤分支)
  本地分支new1再自行刪除

- 搬移遠端儲存庫
修改遠端儲存庫URL

	- 修改

		- .git/config -> origin url:xxx
		- git remote set -url origin xxx

	- 建立新的遠端儲存庫
	- git push --all(不只所在分支,所有分支皆上傳)

## 復原

### 索引

- git reset --mixed HEAD

  --mixed為重置HEAD.Index , 是預設值 , 可不打
  HEAD~1(為前一版的意思)

	- 應用

	  目前索引
	  4.update js(HEAD)
	  3.update css
	  2.add all
	  1.init commit
	  
	  想合併3.4的commit , 但保留工作目錄的原始碼
	  git reset HEAD~2 (回到2)
	  git add .
	  git commit -m "update css  js"

- git reset -p (針對區塊的reset)

### 工作目錄

- git reset --hard HEAD

  重置HEAD.工作目錄.Index

### RESET
MERGE
REBASE

- git reset --hard ORIG_HEAD

  此三類型的commit會被記錄在.git/ORIG_HEAD , 故若MERGE失敗 , 可透過此指令回溯

### 某版本所有變更

- git reset xxx(版本id)

### 特定檔案or資料

- git checkout --xxx(檔名/資料夾)

  索引資料複製回工作目錄

- git checkout xxx(版本id/分支名稱) xxx(檔名/資料夾)

## 差異

### 檔案

### 二進位檔案(圖片)

- git diff --binary

### 兩個版本異動清單&狀態

- git diff --name-only
- git diff --name-status

  M修改  A新增  R更名  D刪除
  git diff --name-status --diff-filter=D xxx xxx(版本id)
  可透過過濾器篩選出兩版本間 , 刪除的檔案

### patch

- 提檔

	- git diff xxx xxx > test.patch

	  若有圖片檔, 要加--binary
	  git diff --binary xxx xxx > test.patch

- 應用

	- git apply test.patch

	  git apply --check test.patch
	  檢查檔案是否有衝突
	  git apply --reverse test.patch
	  退版 , 可自動反向操作(新增檔案即刪除 , 刪檔即新增回來 , --reverse 可縮寫為-R)

## REBASE

### 最近一次的commit

- git commit --amend

### 刪除/編輯歷史版本

- git rebase -i xxx(版本id)

	- 刪除
	- 調整版本順序

		- 衝突

			- 處理
			- 放棄

				- git rebase --abort

	- 編輯

		- 將pick改e
		- 修改檔案 git add .
		- git commit --amend
		- git rebase --continue

	- 插入

		- 將pick改e
		- 修改檔案 git add .
		- git commit -m "xxx"
		- git rebase --continue

	- 合併

		- 壓縮squash

		  假設目前有5個commit , 想將3.4的commit併入2
		  5.aaa
		  4.bbb
		  3.ccc
		  2.ddd
		  1.eee
		  
		  git rebase -i 1(需回到2 , 故選擇上一版id)
		  將3.4的pick都改為squash
		  pick      2
		  squash 3
		  squash 4
		  pick      5

## --no-ff快轉機制關閉
no fast-forward

### no

- 意義上完全不同的分支

### yes

- 遠端&本地分支
origin/master  master

## 索引
(Index)
.git/index
A
B

### 
Tracked Files

## 版本
(Commit)




## 工作目錄
A
B
C
D

### 
Untracked Files

## 檔案內容
＝Stage
≠Unstage

## 各種分支

### 本地分支
master
.git/refs/heads

### 遠端追蹤分支
remotes/origin/master


### 遠端分支
origin/master
.git/refs/remotes/origin

*XMind: ZEN - Trial Version*