modify some codes, so it can tun on windows  

refer [issue](https://github.com/eragonruan/text-detection-ctpn/issues/73)

1. make some change

   change `np.int_t` to `np.intp_t` in line 25 of the file ==lib\utils\cython_nms.pyx==, otherwise appear "ValueError: Buffer dtype mismatch" in step 6

2. update c file

   execute: `cd your_dir\text-detection-cptn-master\lib\utils`  

   execute: `cython bbox.pyx`  

   execute: `cython cython_nms.pyx`  

3. build setuo file as ==setup_new.py==

   ```python
   import numpy as np
   from distutils.core import setup
   from Cython.Build import cythonize
   from distutils.extension import Extension
   numpy_include = np.get_include()
   setup(ext_modules=cythonize("bbox.pyx"),include_dirs=[numpy_include])
   setup(ext_modules=cythonize("cython_nms.pyx"),include_dirs=[numpy_include])  
   ```

4. build `.pyd` file

   execute: `python setup_new.py install`  

   copy `bbox.cp36-win_amd64.pyd and cython_nms.cp36-win_amd64.pyd` to `your_dir\text-detection-ctpn-master\lib\utils `

5. make some changes

   * Set `USE_GPU_NMS ` in the file ==\ctpn\text.yml== as "False" 
   *  Set the `_*C.USE_GPU_NMS` in the file ==\lib\fast_rcnn\config.py== as "False";*
   * Comment out the line "from lib.utils.gpu_nms import gpu_nms" in the file ==\lib\fast_rcnn\nms_wrapper.py==;
   * Comment out the line "from . import gpu_nms" in the file ==\lib\utils_init*.py==; 
   *  change "base_name = image_name.split('/')[-1]" to "base_name = image_name.split('\')[-1]" in line 24 of the file ==ctpn\demo.py==

6. run demo

   execute: cd your_dir\text-detection-ctpn-master   

   execute: python ./ctpn/demo.py 