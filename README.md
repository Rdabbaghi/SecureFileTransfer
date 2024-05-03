# SecureFileTransfer

SecureFileTransfer is a simple file transfer application built using Python and PyQt5, with server-side file storage implemented using the Kademlia distributed hash table (DHT) network. This application allows users to securely transfer files over a network while ensuring the integrity and confidentiality of the transferred files.

## Features

- **Secure File Transfer**: Utilizes SHA-256 hashing for file integrity verification and secure file transfer.
- **User-Friendly Interface**: Provides an intuitive graphical user interface (GUI) for easy file browsing, selection, and transfer.
- **Server-Side Storage**: Implements a file storage server using Kademlia DHT for efficient and decentralized file storage.
- **Hash-Based File Retrieval**: Enables users to search for files by their unique SHA-256 hash values.
- **Clipboard Integration**: Offers convenient copying and pasting of file hashes using the system clipboard.

## Usage

1. **Sending Files**:
   - Click the "Browse" button to select a file for transfer.
   - Click the "Send File" button to securely transfer the selected file to the server.
   - Upon successful transfer, the SHA-256 hash of the file will be displayed for reference.

2. **Searching Files**:
   - Enter the SHA-256 hash of the file you wish to retrieve in the designated field.
   - Click the "Search File" button to find the file corresponding to the provided hash.
   - If the file is found, its location will be displayed; otherwise, an error message will be shown.

3. **Copying and Pasting Hashes**:
   - Use the "Copy" button to copy the SHA-256 hash of a file to the system clipboard.
   - Use the "Paste" button to paste a copied hash into the designated field.

## Installation

1. Clone the repository to your local machine:	
	https://github.com/Rdabbaghi/SecureFileTransfer.git

2. Install the required dependencies using pip:
	pip install -r requirements.txt

3. Run the application:
	python main.py


## Contributing

Contributions are welcome! If you encounter any issues or have suggestions for improvements, please feel free to open an issue or submit a pull request.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
