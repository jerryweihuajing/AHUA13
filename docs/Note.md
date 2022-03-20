# Note

## Python

### 1 Anaconda Command Line

- 查看环境列表：


```
conda env list
```

或

```
conda info --env
```

- 创建conda环境：


```
conda create -n pytorch python=3.8
```

- 删除conda环境：


```
conda remove -n pytorch --all
```

- conda查看channels：


```
conda config --show channels
```

- conda增加channels：


```
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --set show_channel_urls yes
```

- conda删除channels：


```
conda config --remove channels defaults
```

- 更新conda：

```
conda update -n base -c defaults conda
```



### 2 OpenCV

- cv2.error: OpenCV(4.5.5) D:\a\opencv-python\opencv-python\opencv\modules\core\src\batch_distance.cpp:275: error: (-215:Assertion failed) type == src2.type() && src1.cols == src2.cols && (type == CV_32F || type == CV_8U) in function 'cv::batchDistance'解决方法

```
list_descriptor_left = list_descriptor_left.astype(np.uint8)
```



### 3 PyTorch

1. OMP: Error #15: Initializing libiomp5md.dll, but found libiomp5md.dll already initialized.
   OMP: Hint This means that multiple copies of the OpenMP runtime have been linked into the program. That is dangerous, since it can degrade performance or cause incorrect results. The best thing to do is to ensure that only a single OpenMP runtime is linked into the process, e.g. by avoiding static linking of the OpenMP runtime in any library. As an unsafe, unsupported, undocumented workaround you can set the environment variable KMP_DUPLICATE_LIB_OK=TRUE to allow the program to continue to execute, but that may cause crashes or silently produce incorrect results. For more information, please see http://www.intel.com/software/products/support/.

```
os.environ['KMP_DUPLICATE_LIB_OK'] = 'True'
```

## Linux

### 1 Terminal Command Line

- make的等同操作：


```
make .
cmake --build .
```

- error: ‘slots_reference’ was not declared in this scope：


```objectivec
sed -i 's/++11/++14/g' CMakeLists.txt
```

- ./visualizeGeometry: error while loading shared libraries: libpango_windowing.so: cannot open shared object file: No such file or directory：需要添加系统环境变量







