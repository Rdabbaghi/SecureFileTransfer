#requirment libraries:
#        pyQt5
#        kademlia
#        pil
#        pyperclip
# ========================

import sys
from PyQt5.QtWidgets import (
    QApplication,
    QMainWindow,
    QLabel,
    QPushButton,
    QLineEdit,
    QTextEdit,
    QFileDialog,
    QMessageBox
)
from kademlia.network import Server
from hashlib import sha256
import os
import asyncio
import pyperclip


class FileTransferServer(Server):
    """
    A class representing a file transfer server.

    Attributes:
        stored_files (dict): A dictionary to store file hashes and their content.
        file_storage_path (str): The directory to store file contents and index.
        file_index_file (str): The file path to the index file.
    """

    def __init__(self, file_storage_path, *args, **kwargs):
        """
        Initializes the FileTransferServer object.

        Args:
            file_storage_path (str): The directory to store file contents and index.
            *args: Positional arguments to pass to the parent class.
            **kwargs: Keyword arguments to pass to the parent class.
        """
        super().__init__(*args, **kwargs)
        self.stored_files = {}  # Dictionary to store file hashes and their content
        self.file_storage_path = file_storage_path
        self.file_index_file = os.path.join(file_storage_path, "file_index.txt")

        # Load existing file index
        if os.path.exists(self.file_index_file):
            with open(self.file_index_file, "r") as f:
                for line in f:
                    file_hash, file_name = line.strip().split(",")
                    self.stored_files[file_hash] = file_name

    def store_file(self, file_path):
        """
        Stores a file in the server.

        Args:
            file_path (str): The path to the file to be stored.

        Returns:
            str: The hash of the stored file.

        Raises:
            ValueError: If the file already exists.
        """
        with open(file_path, 'rb') as file:
            file_content = file.read()
            file_hash = sha256(file_content).hexdigest()
            file_name = os.path.basename(file_path)

            # Check if the file hash already exists
            if file_hash in self.stored_files:
                raise ValueError("File already exists.")

            self.stored_files[file_hash] = file_name

            # Save file index
            with open(self.file_index_file, "a") as f:
                f.write(f"{file_hash},{file_name}\n")

            # Save file content
            with open(os.path.join(self.file_storage_path, file_hash), "wb") as f:
                f.write(file_content)

            return file_hash

    def find_file(self, file_hash):
        """
        Finds a file in the server.

        Args:
            file_hash (str): The hash of the file to search for.

        Returns:
            str: The path to the found file, or None if not found.
        """
        file_name = self.stored_files.get(file_hash, None)
        if file_name:
            file_path = os.path.join(self.file_storage_path, file_hash)
            return file_path
        else:
            return None


class FileTransferUI(QMainWindow):
    """
    A class representing the user interface for file transfer.
    """

    def __init__(self, server):
        """
        Initializes the FileTransferUI object.

        Args:
            server (FileTransferServer): The file transfer server object.
        """
        super().__init__()
        self.server = server
        self.setWindowTitle("File Transfer App")
        self.setGeometry(100, 100, 600, 400)

        self.init_ui()

    def init_ui(self):
        # Labels
        self.file_path_label = QLabel("File Path:", self)
        self.file_path_label.move(20, 20)

        self.hash_label = QLabel("Hash:", self)
        self.hash_label.move(20, 70)

        # Line Edits
        self.file_path_entry = QLineEdit(self)
        self.file_path_entry.setGeometry(100, 20, 300, 30)

        self.hash_entry = QLineEdit(self)
        self.hash_entry.setGeometry(100, 70, 300, 30)

        # Buttons
        self.browse_button = QPushButton("Browse", self)
        self.browse_button.setGeometry(420, 20, 100, 30)
        self.browse_button.clicked.connect(self.browse_file)

        self.send_button = QPushButton("Send File", self)
        self.send_button.setGeometry(420, 70, 100, 30)
        self.send_button.clicked.connect(self.send_file)

        self.search_button = QPushButton("Search File", self)
        self.search_button.setGeometry(420, 120, 100, 30)
        self.search_button.clicked.connect(self.search_file)

        self.copy_button = QPushButton("Copy", self)
        self.copy_button.setGeometry(420, 170, 100, 30)
        self.copy_button.clicked.connect(self.copy_hash)

        self.paste_button = QPushButton("Paste", self)
        self.paste_button.setGeometry(420, 220, 100, 30)
        self.paste_button.clicked.connect(self.paste_hash)

        # Text Edit
        self.result_text = QTextEdit(self)
        self.result_text.setGeometry(20, 120, 380, 250)

    def browse_file(self):
        file_path, _ = QFileDialog.getOpenFileName(self, "Open File", "", "All Files (*);;Text Files (*.txt)")
        if file_path:
            self.file_path_entry.setText(file_path)

    def send_file(self):
        file_path = self.file_path_entry.text()
        if os.path.exists(file_path):
            try:
                file_hash = self.server.store_file(file_path)
                self.hash_entry.setText(file_hash)
                QMessageBox.information(self, "Success", "File sent successfully.")
            except ValueError as e:
                QMessageBox.critical(self, "Error", str(e))
        else:
            QMessageBox.critical(self, "Error", "File not found.")

    def search_file(self):
        file_hash = self.hash_entry.text()
        if file_hash:
            found_file_path = self.server.find_file(file_hash)
            if found_file_path:
                QMessageBox.information(self, "File Found", f"File found at:\n{found_file_path}")
            else:
                QMessageBox.critical(self, "File Not Found", "File with the given hash not found.")
        else:
            QMessageBox.critical(self, "Error", "Please enter a hash to search for.")

    def copy_hash(self):
        hash_value = self.hash_entry.text()
        pyperclip.copy(hash_value)

    def paste_hash(self):
        hash_value = pyperclip.paste()
        self.hash_entry.setText(hash_value)


if __name__ == "__main__":
    file_storage_path = "file_storage"  # Directory to store file contents and index
    os.makedirs(file_storage_path, exist_ok=True)

    server = FileTransferServer(file_storage_path)
    asyncio.run(server.listen(8468))

    app = QApplication(sys.argv)
    ui = FileTransferUI(server)
    ui.show()
    sys.exit(app.exec_())
