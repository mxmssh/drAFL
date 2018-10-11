# drAFL

Original AFL supports black-box coverage-guided fuzzing using QEMU mode. I highly recommend to try it first and if it doesn't work you can try this tool.

# Usage

You need to specify ```DRRUN_PATH``` to point to ```drrun``` launcher and ```LIBCOV_PATH``` to point to ```libbinafl.so``` coverage DLL. You also need to switch off AFL's fork server (```AFL_NO_FORKSRV=1```) and probably ```AFL_SKIP_BIN_CHECK=1```. See step 5 in the build section below for more details.

# Instrumentation DLL

Instrumentation DLL is a modified version of [winAFL's](https://github.com/googleprojectzero/winafl) coverage DLL created by Ivan Fratric.

# Build

### Step 1. Clone drAFL repo
```
git clone https://github.com/mxmssh/drAFL.git /home/max/drAFL
cd /home/max/drAFL
```

### Step 2. Clone and build DynamoRIO
```
git clone https://github.com/DynamoRIO/dynamorio
mkdir build_dr
cd build_dr/
cmake ../dynamorio/
make -j
cd ..
```
If you have any problems with DynamoRIO compilation check this [page](https://github.com/DynamoRIO/dynamorio/wiki/How-To-Build#linux)

### Step 3. Build coverage tool
```
mkdir build
cd build
cmake ../bin_cov/ -DDynamoRIO_DIR=../build_dr/cmake
make -j
cd ..
```
### Step 4. Build patched AFL
```
cd afl/
make
cd ..
```

### Step 5. Configure environment variables and run the target
```
cd build
mkdir in
mkdir out
echo "AAAA" > in/seed
export DRRUN_PATH=/home/max/drAFL/build_dr/bin64/drrun
export LIBCOV_PATH=/home/max/drAFL/build/libbinafl.so 
export AFL_NO_FORKSRV=1
export AFL_SKIP_BIN_CHECK=1
../afl/afl-fuzz -m 500 -i in -o out -- ./afl_test @@
```
In case of ```afl_test``` you should expect 25-30 exec/sec and 1 unique crash in 2-3 minutes.
