### LEO NEW PROGRAM STRUCTURE 

![Screenshot from 2024-05-03 03-35-10](https://github.com/Elexy101/zero-to-zk/assets/24855083/00023340-530e-408c-9fd8-fd1e1139578a)


### Build Structure
- **<program_name>.aleo**: This directory stores the compiled output of the Leo program. It contains the generated Aleo assembly code, which is ready to be deployed onto the Aleo blockchain.
- **program.json**: This file serves as a manifest for the Leo program. It includes metadata such as the program name, version, description, and license. This allows for better organization and management of the program's details.

### Src Structure
- The `src` directory shows the source code of the Leo program. This includes the main logic and functionality of the program, written in the Leo programming language.
- The `main.leo` file is the entry point for the program and is automatically generated when creating a new program. Developers can edit this file to define the behavior of their program.

### Outputs Directory
- The `outputs` directory is currently empty by default when generated. It is intended to store any output files or artifacts generated during the build process. This can include logs, debug information, or any other files related to the program. In the old version Leo program, there was `inputs` folder, but in the current version, there is no `inputs` required 

### Environment Variables (.env)
- The `.env` file is used to configure the environment for the Leo program. It includes settings such as the network configuration (e.g., Testnet or Mainnet) and the private key of the `self.caller` address. Changing these settings can affect how the program interacts within the Aleo blockchain. When running Leo program, make sure your address is tied up same to the private key of `self.caller`.

### Imports
- The `imports` feature in Leo program v11.0 allows developers to import and use code from other Leo programs. This is useful for reusing code and managing dependencies.
- To use imports, developers create an `imports` folder in their program directory and place the imported programs inside this folder. Each imported program should have its own folder.
- When trying importing a program, developers use the `leo new <imported_program_name>` command to create the necessary files in the program. This includes the `build`, `src`, `outputs`, and `program.json` files for the imported program.
- Multiple imports can be used in a single program, each with its own folder within `imports` folder. This allows for modular and reusable code, improving code organization and maintainability.

### Dependencies in Imports and program.json
Dependencies are specified in the program.json file of each program, including the main program and any imported programs.
The program.json file contains information about the program and its dependencies. It includes:
- "dependencies": This field specifies the dependencies of the program. It lists the names, location, network and path of the programs that this program depends on.
- Example: "dependencies": { "name": "test.aleo",
      "location": "network",
      "network": "testnet3",
      "path": null }


### Gitignore
- The `.gitignore` file is used to specify which files and directories should be ignored by version control systems like Git. This helps keep the repository clean and prevents sensitive information from being exposed.
- Common entries in the `.gitignore` file for a Leo program include the `build` directory (which contains compiled code), the `.env` file (which contains sensitive information), and any other temporary or generated files that should not be included in the repository.