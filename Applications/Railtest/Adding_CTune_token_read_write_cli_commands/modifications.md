---
sort: 2
---

## Modification to Ctune token cli commands

  1.  Select the project file (.slcp)

  2. modify your app.c file with this content. It adds several cli command. 
  WARNING: EraseCtuneTokenPage() function suppose the User Data page of the Flash is empty.
  if you save additionnal tokens or any data in the User data page, you will need to save before erasing and writing them back, therefore modify the function to do so.
  
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
    #include "btl_interface.h"
    #include "em_msc.h"

    void cliSeparatorHack(sl_cli_command_arg_t *arguments);

    void GetCtuneToken (sl_cli_command_arg_t *args)
    {
      uint32_t *address = (uint32_t*)0xfe00100; //CTUNE base

      printf("TOKEN_MFG_CTUNE = 0x%x\n", (uint32_t) (*address));
    }

    void SetCtuneToken (sl_cli_command_arg_t *args)
    {
      uint32_t *address = (uint32_t*)0xfe00100; //CTUNE base
      uint32_t value = sl_cli_get_argument_uint32(args, 0);
      int8_t status;

      if ((*address) == 0xffffffff)
        {
        status = MSC_WriteWord(address, &value, 4);
        if (status == 0)
            printf("TOKEN_MFG_CTUNE has been set with value 0x%x\n",(int) value);
        else
          printf("error %d\n",status);
        }
      else
        printf("ERROR: TOKEN_MFG_CTUNE is already set to 0x%x\n", *address);

    }

    void EraseCtuneTokenPage (sl_cli_command_arg_t *args)
    {
      uint32_t * userDataPage = (uint32_t *) USERDATA_BASE;
      int8_t status;

      status = MSC_ErasePage(userDataPage);
      if (status == 0)
        printf("TOKEN_MFG_CTUNE has been erased\n");
      else
        printf("error %d\n",status);
    }

    static const sl_cli_command_info_t cli_cmd___________________________ = \
      SL_CLI_COMMAND(cliSeparatorHack,
                    "",
                      "",
                    {SL_CLI_ARG_END, });

    void cliSeparatorHack(sl_cli_command_arg_t *arguments);

    static const sl_cli_command_info_t cli_cmd______Ctune_Technicolor____ = \
      SL_CLI_COMMAND(cliSeparatorHack,
                    "",
                      "",
                    {SL_CLI_ARG_END, });

    static const sl_cli_command_info_t cli_cmd__bootload = \
      SL_CLI_COMMAND(bootload,
                    "call the standalone bootloader",
                    "",
                    {SL_CLI_ARG_END, });

    static const sl_cli_command_info_t cli_cmd__GetCtuneToken = \
      SL_CLI_COMMAND(GetCtuneToken,
                    "Read current Ctune value",
                    "",
                    {SL_CLI_ARG_END, });

    static const sl_cli_command_info_t cli_cmd__SetCtuneToken = \
      SL_CLI_COMMAND(SetCtuneToken,
                    "Set Ctune Token",
                    "value0" SL_CLI_UNIT_SEPARATOR,
                    {SL_CLI_ARG_UINT32, SL_CLI_ARG_END, });

    static const sl_cli_command_info_t cli_cmd__EraseCtuneTokenPage = \
      SL_CLI_COMMAND(EraseCtuneTokenPage,
                    "Erase Ctune token Page",
                    "",
                    {SL_CLI_ARG_END, });

    // Define the command table

    const sl_cli_command_entry_t cli_custom_command_table[] = {
      { "___________________________", &cli_cmd___________________________, false },
      { "GetCtuneToken", &cli_cmd__GetCtuneToken, false },
      { "SetCtuneToken", &cli_cmd__SetCtuneToken, false },
      { "EraseCtuneTokenPage", &cli_cmd__EraseCtuneTokenPage, false },
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