# Configure the Terrain_Trees_sea_ice library in the gsapp13 cluster

### 1. Download the github repository
   ```
   git clone https://github.com/UMDGeoVis/Terrain_Trees.git
   ```
### 2. Uncompress the file (not necessary)
   ```
   tar -xvzf filename.tar.gz
   ```
### 3. Checkout to switch to the features/sea_ice_analysis branch
   ```
   git checkout features/sea_ice_analysis
   ```
### 4. Download Eigen3 put it under the main directory of this repository
   download:
   ```
   wget https://gitlab.com/libeigen/eigen/-/archive/3.4.0/eigen-3.4.0.tar.gz
   ```
   uncompress:
   ```
   tar -xvzf eigen-3.4.0.tar.gz
   ```
   rename:
   ```
   mv eigen-3.4.0 eigen
   ```
### 5. Modify the CMakeLists.txt file in the main directory of this repository  
  (1) modification related to Eigen3  
  + replace
      ```
      #new:
      find_package (Eigen3 3.3 REQUIRED)
      include_directories(/usr/include/eigen3/)
      #end
      ```      
      with      
      ```
      # Correct way to include Eigen (placed in the project root folder)
      include_directories(${PROJECT_SOURCE_DIR}/eigen)
      ```
   + replace
     ```
     set(CMAKE_CXX_FLAGS "-O3 -march=native -std=c++11 -fopenmp -I /home/songy/eigen") ## Optimize
     ```
     with
     ```
     set(CMAKE_CXX_FLAGS "-O3 -march=native -std=c++11 -fopenmp -I /gpfs/data1/cgis1gp/yuehui/codes/Terrain_Trees_seaice_11242025/eigen") ## Optimize
     ```
   (2) modification related to boost
   + replace
     ```
     # Boost directory:
     include_directories(/opt/homebrew/opt/boost@1.81/include)
     # include_directories(${Boost_INCLUDE_DIR})
     ```
     with
     ```
     # Boost configuration: Use the environment variable set by the loaded module
     set(Boost_INCLUDE_DIR /apps/boost/1.84.0/include)
     set(Boost_LIBRARY_DIR /apps/boost/1.84.0/lib)
     include_directories(${Boost_INCLUDE_DIR})
     ```
   + delete (maybe not necessary)
     ```
     # find_package(Boost REQUIRED)
     #~ find_package(Doxygen REQUIRED)
     ```

### 6. Load basic modules
   ```
   module load boost/1.84.0
   module load qt/5.8.0
   module load gcc/12.3.0
   module load cgal/4.9
   ```
   Do not load cmake/3.10.2, instead, use cmake-3.26.0
   Before running cmake, export the following to the path:
   ```
   export LD_LIBRARY_PATH=/apps/python/3.6/anaconda/pkgs/mpfr-3.1.5-h11a74b3_2/lib:$LD_LIBRARY_PATH
   ```
   The following is good to run, but maybe not necessary:
   ```
   export LD_LIBRARY_PATH=/apps/gcc/12.3.0/lib64:/apps/gcc/12.3.0/lib:$LD_LIBRARY_PATH
   ```
### 7. Run cmake and make, but with the following modern commands.
   (1) Create a "build" directory and run "cmake CMakeLists.txt"
      ```
      rm -rf build
      /gpfs/data1/cgis1gp/yuehui/cmake-3.26.0/bin/cmake -S . -B build \
        -DCMAKE_C_COMPILER=/apps/gcc/12.3.0/bin/gcc \
        -DCMAKE_CXX_COMPILER=/apps/gcc/12.3.0/bin/g++
      ```
   (2) Run "make", which is the "build"
      ```
      /gpfs/data1/cgis1gp/yuehui/cmake-3.26.0/bin/cmake --build build -j
      ```
   (3) Run "make sea_ice"
      ```
      /gpfs/data1/cgis1gp/yuehui/cmake-3.26.0/bin/cmake --build build --target sea_ice -j
      ```
### 8. Compute ice ridges
   ```
   ./build/bin/test_ridge_extraction  /gpfs/data1/cgis1gp/yuehui/data/Part3_TopoSim/Terrain_trees_seaice_test/ALS_LIB_20190410T174554_181213_small_crop_tiny_test_as_100000.off 20 0.5 -0.31 0 -r 0.08
   ```

### 9. Interpolate ICESat-2 points in a TIN
   ```
   ./test_point_interpolation /gpfs/data1/cgis1gp/yuehui/data/Part3_TopoSim/ALS_L1B_20190410T174554_181213_3/ALS_L1B_20190410T174554_181213_3_as_100000.off 20 -0.31 /gpfs/data1/cgis1gp/yuehui/data/Part3_TopoSim/UMD_data/strong_beam/UMDRDA_ATL03_20190410152144_01890303_007_01_gt2l_DTU21_Elevation_epsg3413_drift_xy_02132026_quater_600_crop.txt
   ```

