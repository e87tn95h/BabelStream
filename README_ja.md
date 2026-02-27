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

```cmd
REM - CMake によるビルド設定
cmake -G Ninja -S . -B cmake-build-sycl2020-usm -DCMAKE_BUILD_TYPE=Release -DMODEL=sycl2020-usm -DSYCL_COMPILER=ONEAPI-ICPX

REM - CMake 経由のビルド
cmake --build cmake-build-sycl2020-usm

REM - 生成されたプログラム (実行可能ファイル) の実行
cmake-build-sycl2020-usm\sycl2020-usm-stream.exe --float --arraysize 134217728
```

```cmd
REM - 実行時のデバイス指定: CPU
set ONEAPI_DEVICE_SELECTOR=opencl:cpu
REM - 実行時のデバイス指定: GPU (インテル)
set ONEAPI_DEVICE_SELECTOR=level_zero:gpu
REM - 実行時のデバイス指定: 無指定
set ONEAPI_DEVICE_SELECTOR=
```

## デバッガーの適用

## プロファイラーの適用
