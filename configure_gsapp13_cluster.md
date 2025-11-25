# Configure the Terrain_Trees_sea_ice library in the gsapp13 cluster

### 1. Download the github repository
   ```
   git clone https://github.com/UMDGeoVis/Terrain_Trees.git
   ```
### 2. Uncompress the file
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
     
