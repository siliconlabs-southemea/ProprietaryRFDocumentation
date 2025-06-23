---
sort: 2
---
## Modification to add Matter DAC readback cli command

1. Select the project file (.slcp)
2. modify your app.c file with this content. It adds the cli command and its function call.

```c
     /***************************************************************************//**
    * @file
    * @brief Application-level Functions
    *******************************************************************************
    * # License
    * <b>Copyright 2019 Silicon Laboratories Inc. www.silabs.com</b>
    *******************************************************************************
    *
    * SPDX-License-Identifier: Zlib
    *
    * The licensor of this software is Silicon Laboratories Inc.
    *
    * This software is provided 'as-is', without any express or implied
    * warranty. In no event will the authors be held liable for any damages
    * arising from the use of this software.
    *
    * Permission is granted to anyone to use this software for any purpose,
    * including commercial applications, and to alter it and redistribute it
    * freely, subject to the following restrictions:
    *
    * 1. The origin of this software must not be misrepresented; you must not
    *    claim that you wrote the original software. If you use this software
    *    in a product, an acknowledgment in the product documentation would be
    *    appreciated but is not required.
    * 2. Altered source versions must be plainly marked as such, and must not be
    *    misrepresented as being the original software.
    * 3. This notice may not be removed or altered from any source distribution.
    *
    ******************************************************************************/
    #include "app.h"
    #include "sl_cli.h"
    #include "sl_cli_config.h"
    #include "sl_cli_command.h"
    #include "sl_cli_arguments.h"
    #include "sl_se_manager_util.h"
    #include "em_msc.h"

    #define DAC_ADDR 0x0817E000
    #define DAC_SIZE 1536u

    volatile uint32_t *dac_ptr = (uint32_t *)DAC_ADDR;
    uint8_t dac_array[DAC_SIZE] = {0u};


    sl_se_command_context_t cmd_ctx;

    void cliSeparatorHack(sl_cli_command_arg_t *arguments);

    void get_dac(sl_cli_command_arg_t *args)
     {
      memcpy((void *)dac_array, (void *)dac_ptr, DAC_SIZE);

      responsePrintStart(sl_cli_get_command_string(args, 0));
      printf("\n");
      for (uint16_t i = 0; i < DAC_SIZE; i++)
  	{
    	 //responsePrintContinue("%X ", (uint8_t)dac_array[i]);
    	 printf("%2.2X ", (uint8_t)dac_array[i]);
  	}
      responsePrintEnd("\nstatus: Success");
     }

    static const sl_cli_command_info_t cli_cmd___________________________ = \
      SL_CLI_COMMAND(cliSeparatorHack,
                    "",
                      "",
                    {SL_CLI_ARG_END, });

    void cliSeparatorHack(sl_cli_command_arg_t *arguments);


    static const sl_cli_command_info_t cli_cmd__GetDAC = \
  	SL_CLI_COMMAND(get_dac,
                 "Get DAC key",
                  "",
                 {SL_CLI_ARG_END, });

    // Define the command table

    const sl_cli_command_entry_t cli_custom_command_table[] = {
      { "___________________________", &cli_cmd___________________________, false },
      { "GetDAC", &cli_cmd__GetDAC, false },
      { NULL, NULL, false },
    };

    // define a command group that will contain the command table and be added to the existing CLI instance

    // created by the UC editor CLI component

    sl_cli_command_group_t cli_custom_command_group =
    {
      { NULL },
      false,
      cli_custom_command_table
    };

    extern sl_cli_handle_t sl_cli_inst0_handle;

    void app_init(void)
    {
      sl_cli_command_add_command_group(sl_cli_inst0_handle, &cli_custom_command_group);
    }

    void app_process_action(void)
    {
    }

```

Once you have done all of the above, your project is ready and you can compile and flash it.

Example:

```bash
> getDAC 
{{(getDAC)} 30 82 01 DD 30 82 01 83 A0 03 02 01 02 02 08 38 05 30 1E 26 C9 62 89 30 0A 06 08 2A 86 48 CE 3D 04 03 02 30 41 31 13 30 11 06 03 55 04 03 0C 0A 4D 61 74 74 65 72 20 50 41 49 31 14 30 12 06 0A 2B 06 01 04 01 82 A2 7C 02 01 0C 04 31 30 34 39 31 14 30 12 06 0A 2B 06 01 04 01 82 A2 7C 02 02 0C 04 38 30 30 35 30 1E 17 0D 32 34 30 32 31 32 30 30 30 30 30 30 5A 17 0D 33 34 30 32 31 38 32 33 35 39 35 39 5A 30 46 31 18 30 16 06 03 55 04 03 0C 0F 4D 61 74 74 65 72 20 4C 69 67 68 74 20 23 31 31 14 30 12 06 0A 2B 06 01 04 01 82 A2 7C 02 01 0C 04 31 30 34 39 31 14 30 12 06 0A 2B 06 01 04 01 82 A2 7C 02 02 0C 04 38 30 30 35 30 59 30 13 06 07 2A 86 48 CE 3D 02 01 06 08 2A 86 48 CE 3D 03 01 07 03 42 00 04 BC C3 89 AD DC DA 74 7B 56 C5 C1 E2 C7 7C A4 AE 1D 02 1C 8A 85 B9 2A F2 8C 8F BC 73 CD 05 82 9A 44 79 D7 AA BF 66 E7 DC 56 8D 74 28 98 DA FA 0E 2C 3D DC D1 68 E3 01 E2 77 61 F1 C1 F1 11 32 46 A3 60 30 5E 30 0C 06 03 55 1D 13 01 01 FF 04 02 30 00 30 0E 06 03 55 1D 0F 01 01 FF 04 04 03 02 07 80 30 1D 06 03 55 1D 0E 04 16 04 14 04 A3 C8 70 1B F6 3C 74 D0 D4 9B 32 42 38 9E 0C DB 16 5D D2 30 1F 06 03 55 1D 23 04 18 30 16 80 14 1F 53 69 6C 07 55 A6 0B 88 AE 4F E9 06 74 6D 58 B5 45 36 AD 30 0A 06 08 2A 86 48 CE 3D 04 03 02 03 48 00 30 45 02 21 00 D7 70 AA 64 3F CD 0B 30 95 E4 93 8A 85 89 C0 84 4C 9E 95 44 9A C6 11 26 24 8B B0 99 78 14 2E DC 02 20 11 DB 62 B4 9C 5A 68 8D FF 96 7B 88 1D 07 AC AC B9 15 1F 4E E4 03 35 00 5C 8B A1 7B C0 EF 8A 2C FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF 30 82 01 B7 30 82 01 5D A0 03 02 01 02 02 08 79 E6 40 0B F1 FB 11 15 30 0A 06 08 2A 86 48 CE 3D 04 03 02 30 1A 31 18 30 16 06 03 55 04 03 0C 0F 4D 61 74 74 65 72 20 54 65 73 74 20 50 41 41 30 1E 17 0D 32 34 30 32 31 32 30 30 30 30 30 30 5A 17 0D 33 34 30 32 31 38 32 33 35 39 35 39 5A 30 41 31 13 30 11 06 03 55 04 03 0C 0A 4D 61 74 74 65 72 20 50 41 49 31 14 30 12 06 0A 2B 06 01 04 01 82 A2 7C 02 01 0C 04 31 30 34 39 31 14 30 12 06 0A 2B 06 01 04 01 82 A2 7C 02 02 0C 04 38 30 30 35 30 59 30 13 06 07 2A 86 48 CE 3D 02 01 06 08 2A 86 48 CE 3D 03 01 07 03 42 00 04 0E 4C E7 6C 85 08 B0 EE E4 96 E9 70 F0 A3 CF FF 77 3B F9 0E 62 C8 75 7C F8 29 0A 1D 57 6C DE 38 D1 2D 18 22 56 88 32 F1 36 2C 3F 71 FE BE C0 19 95 EB 64 B2 D0 93 AA 5A 72 9F C5 EF DC 47 10 15 A3 66 30 64 30 12 06 03 55 1D 13 01 01 FF 04 08 30 06 01 01 FF 02 01 00 30 0E 06 03 55 1D 0F 01 01 FF 04 04 03 02 01 06 30 1D 06 03 55 1D 0E 04 16 04 14 1F 53 69 6C 07 55 A6 0B 88 AE 4F E9 06 74 6D 58 B5 45 36 AD 30 1F 06 03 55 1D 23 04 18 30 16 80 14 78 5C E7 05 B8 6B 8F 4E 6F C7 93 AA 60 CB 43 EA 69 68 82 D5 30 0A 06 08 2A 86 48 CE 3D 04 03 02 03 48 00 30 45 02 20 47 04 73 BB F3 EC DA 15 DE 7E 36 23 70 4D 28 A9 DC A0 E3 0E 0B A4 B0 33 18 15 3D 3A 1D 14 27 CC 02 21 00 9B AE 03 C4 BA 3E 03 DA 69 D3 0C 8B ED 2C 0E B1 E7 F1 D3 DB C5 D0 9F 71 CB EF ED 0D 4F 4F 84 98 FF FF FF FF FF 30 81 F1 06 09 2A 86 48 86 F7 0D 01 07 02 A0 81 E3 30 81 E0 02 01 03 31 0D 30 0B 06 09 60 86 48 01 65 03 04 02 01 30 4C 06 09 2A 86 48 86 F7 0D 01 07 01 A0 3F 04 3D 15 24 00 01 25 01 49 10 36 02 05 05 80 18 24 03 16 2C 04 13 5A 49 47 32 30 31 34 32 5A 42 33 33 30 30 30 33 2D 32 34 24 05 00 24 06 00 25 07 57 02 24 08 00 25 09 49 10 25 0A 05 80 18 31 7E 30 7C 02 01 03 80 14 62 FA 82 33 59 AC FA A9 96 3E 1C FA 14 0A DD F5 04 F3 71 60 30 0B 06 09 60 86 48 01 65 03 04 02 01 30 0A 06 08 2A 86 48 CE 3D 04 03 02 04 48 30 46 02 21 00 B7 E6 5F 92 1B 86 6C FC 65 56 BA E7 59 D2 5C 31 D0 B8 BD 7B 84 A8 96 98 7D E0 F5 D6 38 71 DD 76 02 21 00 F4 AE 5F 39 FF D9 96 34 1C FF 76 3E DA 70 F5 96 AB 5A 0B EA 63 F8 7B BC 79 9A 82 4D 94 8C D2 76 FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF {status: Success}} 
>
```
