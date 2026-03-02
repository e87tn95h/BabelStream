# BabelStream

## 変更点

> プログラミング・モデルは sycl2020-usm のみ確認

- Windows OS でのビルドを有効化

## ビルドと実行

### ビルドと実行: Windows (コマンド プロンプト)

```cmd
REM - Intel oneAPI (Base or HPC) Toolkit の有効化
call "%programfiles(x86)%\Intel\oneAPI\setvars"

REM - または Intel oneAPI C++/DPC++ Compiler 単体の有効化
call "%programfiles(x86)%\intel\oneapi\compiler\latest\env\vars"
```

#### Windows - sycl2020-usm

```cmd
REM - CMake によるビルド設定
cmake -G Ninja -S . -B cmake-build-sycl2020-usm -DCMAKE_BUILD_TYPE=Release -DMODEL=sycl2020-usm -DSYCL_COMPILER=ONEAPI-ICPX -DCXX_EXTRA_FLAGS=""

REM - CMake 経由のビルド
cmake --build cmake-build-sycl2020-usm

REM - 生成されたプログラム (実行可能ファイル) の実行
cmake-build-sycl2020-usm\sycl2020-usm-stream.exe --float --arraysize 134217728
```

`ONEAPI_DEVICE_SELECTOR` 環境変数: デフォルトの SYCL デバイスを指定する拡張機能

```cmd
REM - 実行時のデバイス指定: CPU
set ONEAPI_DEVICE_SELECTOR=opencl:cpu
REM - 実行時のデバイス指定: GPU (インテル)
set ONEAPI_DEVICE_SELECTOR=level_zero:gpu
REM - 実行時のデバイス指定: 無指定
set ONEAPI_DEVICE_SELECTOR=
```
#### Windows - omp

```cmd
REM - CMake によるビルド設定
cmake -G Ninja -S . -B cmake-build-omp -DCMAKE_BUILD_TYPE=Release -DMODEL=omp

REM - CMake 経由のビルド
cmake --build cmake-build-omp

REM - 生成されたプログラム (実行可能ファイル) の実行
cmake-build-omp\omp-stream.exe --float --arraysize 134217728
```

#### Windows - omp target (Intel)

```cmd
REM - CMake によるビルド設定
cmake -G Ninja -S . -B cmake-build-omptarget-intel -DCMAKE_BUILD_TYPE=Release -DMODEL=omp -DOFFLOAD=ON -DCXX_EXTRA_FLAGS="/Qopenmp /Qopenmp-targets=spir64"

REM - CMake 経由のビルド
cmake --build cmake-build-omptarget-intel

REM - 生成されたプログラム (実行可能ファイル) の実行
cmake-build-omptarget-intel\omp-stream.exe --float --arraysize 134217728
```

### ビルドと実行: Linux (bash)

```bash
# Intel oneAPI (Base or HPC) Toolkit の有効化
. /opt/intel/oneapi/setvars.sh

# または Intel oneAPI C++/DPC++ Compiler 単体の有効化
. /opt/intel/oneapi/compiler/latest/env/vars.sh
```

```bash
# CMake によるビルド設定
cmake -S . -B cmake-build-sycl2020-usm -DCMAKE_BUILD_TYPE=Release -DMODEL=sycl2020-usm -DSYCL_COMPILER=ONEAPI-ICPX -DCXX_EXTRA_FLAGS=""

# CMake 経由のビルド
cmake --build cmake-build-sycl2020-usm

# 生成されたプログラム (実行可能ファイル) の実行
cmake-build-sycl2020-usm/sycl2020-usm-stream --float --arraysize 134217728
```

`ONEAPI_DEVICE_SELECTOR` 環境変数: デフォルトの SYCL デバイスを指定する拡張機能

```bash
# 実行時のデバイス指定: CPU
export ONEAPI_DEVICE_SELECTOR=opencl:cpu
# 実行時のデバイス指定: GPU (インテル)
export ONEAPI_DEVICE_SELECTOR=level_zero:gpu
# 実行時のデバイス指定: 無指定
unset ONEAPI_DEVICE_SELECTOR
```

## デバッガーの適用

### デバッガーの適用: Windows (コマンド プロンプト)

```cmd
REM - CMake によるビルド設定を更新
cmake -G Ninja -S . -B cmake-build-sycl2020-usm -DCMAKE_BUILD_TYPE=Release -DMODEL=sycl2020-usm -DSYCL_COMPILER=ONEAPI-ICPX -DCXX_EXTRA_FLAGS="/Zi /MDd /Od"

REM - CMake 経由のビルド
cmake --build cmake-build-sycl2020-usm --clean-first

REM - 実行時のデバイス指定: CPU
set ONEAPI_DEVICE_SELECTOR=opencl:cpu
REM - 有効並列数 (コア数) を減らしておく
set DPCPP_CPU_NUM_CUS=2

REM - Microsoft Visual Studio の GUI デバッガーを呼び出す
devenv /debugexe cmake-build-sycl2020-usm\sycl2020-usm-stream.exe --float --arraysize 131072

REM - [デバッグ(D)] → [ステップ イン(L)] を実行すると
REM - プログラムの実行を開始し、main() の先頭でブレーク (一時停止) できる

REM - [ファイル(F)] → [開く(O)] → [ファイル(F)...] から
REM - 関連するソースファイルを開くことができる

REM - [デバッグ(F)] → [ウィンドウ(W)] → [イミディエイト(I)] で
REM - "イミディエイト ウィンドウ" を開くことができる
REM - ブレークした時点における、変数の値表示や式の評価の確認に利用できる
```

### デバッガーの適用: Linux (bash)

```bash
# CMake によるビルド設定を更新
cmake -S . -B cmake-build-sycl2020-usm -DCMAKE_BUILD_TYPE=Release -DMODEL=sycl2020-usm -DSYCL_COMPILER=ONEAPI-ICPX -DCXX_EXTRA_FLAGS="-g -O0"

# CMake 経由のビルド
cmake --build cmake-build-sycl2020-usm --clean-first

# 実行時のデバイス指定: CPU
export ONEAPI_DEVICE_SELECTOR=opencl:cpu
# 有効並列数 (コア数) を減らしておく
export DPCPP_CPU_NUM_CUS=2

# GDB を呼び出す
gdb-oneapi --args cmake-build-sycl2020-usm/sycl2020-usm-stream --float --arraysize 131072
```

```gdb
# GDB はコマンドベースのデバッガー

# 関数名 (ここでは main) によるブレークポイント設定
break main
# プログラムの実行を開始
run
# 停止点周辺のソースコードを表示
list
# ソースコード行によるブレークポイントを設定
break src/sycl2020-usm/SYCLStream2020.cpp:183
# 次のブレークポイントまで実行を継続
continue
# 停止点周辺のソースコードを表示
list
# 指定した式や変数 (ここでは initB) の内容を表示
print initB
# ソースコード行によるブレークポイントを削除
clear src/sycl2020-usm/SYCLStream2020.cpp:183
# デバッグ終了 (プログラムの実行を停止)
quit
```

## プロファイラーの適用

### プロファイラーの適用: Windows (コマンド プロンプト)

```cmd
REM - CMake によるビルド設定を更新
cmake -G Ninja -S . -B cmake-build-sycl2020-usm -DCMAKE_BUILD_TYPE=Release -DMODEL=sycl2020-usm -DSYCL_COMPILER=ONEAPI-ICPX -DCXX_EXTRA_FLAGS="-gline-tables-only /clang:-fdebug-info-for-profiling"

REM - CMake 経由のビルド
cmake --build cmake-build-sycl2020-usm --clean-first
```

```cmd
REM - Intel oneAPI (Base or HPC) Toolkit の有効化
call "%programfiles(x86)%\Intel\oneAPI\setvars"

REM - 実行時のデバイス指定: CPU
set ONEAPI_DEVICE_SELECTOR=opencl:cpu
set DPCPP_CPU_NUM_CUS=

REM - Intel VTune Profiler による情報収集を有効にしてプログラムを実行
vtune -collect gpu-offload -result-dir=vtune-profiles/r@@@{at} -quiet -- cmake-build-sycl2020-usm\sycl2020-usm-stream.exe --float --arraysize 134217728

REM - 実行時のデバイス指定: GPU (インテル)
set ONEAPI_DEVICE_SELECTOR=level_zero:gpu

REM - Intel VTune Profiler による情報収集を有効にしてプログラムを実行
vtune -collect gpu-offload -result-dir=vtune-profiles/r@@@{at} -quiet -- cmake-build-sycl2020-usm\sycl2020-usm-stream.exe --float --arraysize 134217728

REM - Intel VTune Profiler GUI で結果を見る
vtune-gui --suppress-automatic-help-tours vtune-profiles
```

```bash
# Intel VTune Profiler Server の起動
vtune-backend --suppress-automatic-help-tours --web-port=38086 --data-directory=vtune-profiles

# 表示されるメッセージの URL をウェブブラウザーで開くことで結果を見る
# > VTune Profiler GUI is accessible via https://127.0.0.1:38086/??one-time-token=XXXXXXXX

# ブラウザーでの初回アクセス時にはパスフレーズ設定がある
# 8 文字以上、記号数字を含む適当な文字列を用意して入力する (有効である例: profile1!)
```

### プロファイラーの適用: Linux (bash)

```bash
# CMake によるビルド設定を更新
cmake -S . -B cmake-build-sycl2020-usm -DCMAKE_BUILD_TYPE=Release -DMODEL=sycl2020-usm -DSYCL_COMPILER=ONEAPI-ICPX -DCXX_EXTRA_FLAGS="-gline-tables-only -fdebug-info-for-profiling"

# CMake 経由のビルド
cmake --build cmake-build-sycl2020-usm --clean-first
```

```bash
# Linux システムの保護機能により、一般ユーザーによる Intel VTune Profiler のプロファイルは妨げられやすい
# 実行できなかった場合のエラーメッセージをよく調べるか、初めから root ユーザーで実行する
sudo su

# Intel oneAPI (Base or HPC) Toolkit の有効化
. /opt/intel/oneapi/setvars.sh

# 実行時のデバイス指定: CPU
export ONEAPI_DEVICE_SELECTOR=opencl:cpu
unset DPCPP_CPU_NUM_CUS

# Intel VTune Profiler による情報収集を有効にしてプログラムを実行
vtune -collect gpu-offload -result-dir=vtune-profiles/r@@@{at} -quiet -- cmake-build-sycl2020-usm/sycl2020-usm-stream --float --arraysize 134217728

# 実行時のデバイス指定: GPU (インテル)
export ONEAPI_DEVICE_SELECTOR=level_zero:gpu

# Intel VTune Profiler による情報収集を有効にしてプログラムを実行
vtune -collect gpu-offload -result-dir=vtune-profiles/r@@@{at} -quiet -- cmake-build-sycl2020-usm/sycl2020-usm-stream --float --arraysize 134217728
```

```bash
# リモート Linux システムには、ポートフォワードを有効にしてログインしておく
# 例) ssh -L 38086:localhost:38086 developer@remote-server

# Intel VTune Profiler Server の起動
vtune-backend --suppress-automatic-help-tours --web-port=38086 --data-directory=vtune-profiles

# 表示されるメッセージの URL をウェブブラウザーで開くことで結果を見る
# > VTune Profiler GUI is accessible via https://127.0.0.1:38086/??one-time-token=XXXXXXXX

# ブラウザーでの初回アクセス時にはパスフレーズ設定がある
# 8 文字以上、記号数字を含む適当な文字列を用意して入力する (有効である例: profile1!)
```
