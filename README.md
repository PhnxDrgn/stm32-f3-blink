# stm32-f3-blink
STM32 F3 NUCLEO board blink. This repo serves as a base project example for the NUCLEO-F303K8.

# Getting Started
## Requirements:
- [Docker Desktop](https://www.docker.com/products/docker-desktop/): Tool used to make containers for consistent development environment.
- [STM32CubeMX](https://www.st.com/en/development-tools/stm32cubemx.html): GUI for making board support packages (BSP)
- [STM32CubeProgrammer](https://www.st.com/en/development-tools/stm32cubeprog.html): Tool to read/write to STM32 memory. Used for flashing.

Optional
- [VS Code](https://code.visualstudio.com/): Open-source source code editor with tons of extensions.

VS Code Extensions (Can be installed within VS Code)
- [VS Code CMake Tools Extention](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cmake-tools): A tool that lets you build/configure CMake projects without using the command line.
- [Dev Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers): An extension that lets you work on the source code within the Docker container and use the terminal inside the container.

## Generateing board support package files
1. Open STM32BCubeMX
2. Load in `nucleo_f3.ioc` from `bsp/nucleo_f3`
    - This project has the following parameters set:
        - `PB3` set as a `GPIO_Output` and named `LED`
            - This pin is the LED pin on the NUCLEO-F303K8 board
        - The Toolchain/IDE option set to `Makefile`
        - Code Generator STM32Cube MCU packages and embedded software packs set to `Copy only the necessary library files`
            - This is to prevent it from including all the library files
3. Generate Code
    - This should generate files in the `bsp/nucleo_f3` directory

## Setting up Docker
Using the `docker_start.sh` script requires the ability to use bash. Linux systems can run it natively but Windows might require some setup. I personally use open a GitBash terminal in VS code to run it.

Running the `docker_start.sh` script:
```
./docker_start.sh
```
The script runs docker in detetched mode (-d) so if you want to attach to it, you can run `docker attach stm32-f3-blink`. The script also names the container the same name as the directory.

### Developing in Docker

## Setting up functions in the bsp `main.c`
1. The `app.c` file calls a function named `millis` and `toggleLed`. These functions are not yet set up.
2. Navigate to `main.h` located in `bsp/nucleo_f3/Core/Inc`.
3. Between the comment `USER CODE BEGIN EFP` add in prototypes for those functions. It should look something like
    ```
    /* USER CODE BEGIN EFP */
    uint32_t millis();
    void toggleLed();
    /* USER CODE END EFP */
    ```
4. Navigate to `main.c` located in `bsp/nucleo_f3/Core/Src`.
5. Add in the functions that were prototyped in the `main.h` file under the section `USER CODE BEGIN 0`. It should look like
    ```
    /* USER CODE BEGIN 0 */
    uint32_t millis()
    {
    return HAL_GetTick();
    }

    void toggleLed()
    {
    HAL_GPIO_TogglePin(LED_GPIO_Port, LED_Pin);
    }
    /* USER CODE END 0 */
    ```
6. Include `app.h` in the `main.c` file under `USER CODE BEGIN Includes`. It should look like
    ```
    /* USER CODE BEGIN Includes */
    #include "app.h"
    /* USER CODE END Includes */
    ```
7. Finally, right above `USER CODE END WHILE` in the same `main.c` file. Add in the app function. It should look like
    ```
    /* Infinite loop */
    /* USER CODE BEGIN WHILE */
    while (1)
    {
        app();
        /* USER CODE END WHILE */

        /* USER CODE BEGIN 3 */
    }
    /* USER CODE END 3 */
    ```

## Building the executable
### Building using VS Code CMake extention
1. Under `PROJECT OUTLINE` there should be the `blink` project name. Click on `Configure`.
2. Under the project there should be a `blink.elf (Executable)`. Click on `Build`
3. The `.elf`, `.hex`, and `.bin` files should be generated to the `build` directory.

### Building manually
1. Configuring
    ```
    mkdir build
    cd build
    cmake ..
    ```
2. Building
    ```
    cmake --build .
    ```