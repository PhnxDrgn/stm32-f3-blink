# stm32-f3-blink
STM32 F3 NUCLEO board blink. This repo serves as a base project example for the NUCLEO-F303K8.

# Getting Started
## Requirements:
- [Docker Desktop](https://www.docker.com/products/docker-desktop/): Tool used to make containers for consistent development environment.
- [STM32CubeMX](https://www.st.com/en/development-tools/stm32cubemx.html): GUI for making board support packages (BSP)
- [STM32CubeProgrammer](https://www.st.com/en/development-tools/stm32cubeprog.html): Tool to read/write to STM32 memory. Used for flashing.

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
Talk about running docker then about using Dev Container in VS Code

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