# day-37-task
const express = require('express');
const fs = require('fs');
const path = require('path');
const app = express();

const PORT = process.env.PORT || 3000;
const folderPath = path.join(__dirname, 'text_files');

// Ensure the folder exists
if (!fs.existsSync(folderPath)) {
    fs.mkdirSync(folderPath);
}

// Endpoint to create a text file with the current timestamp as content
app.post('/create-timestamp-file', (req, res) => {
    const currentDateTime = new Date().toISOString().replace(/:/g, '-');
    const fileName = `${currentDateTime}.txt`;
    const filePath = path.join(folderPath, fileName);
    const content = `Current Timestamp: ${new Date().toISOString()}`;

    fs.writeFile(filePath, content, (err) => {
        if (err) {
            return res.status(500).json({ error: 'Error writing file' });
        }
        res.json({ message: `File created successfully: ${fileName}` });
    });
});

// Endpoint to retrieve all the text files in the folder
app.get('/list-timestamp-files', (req, res) => {
    fs.readdir(folderPath, (err, files) => {
        if (err) {
            return res.status(500).json({ error: 'Error reading directory' });
        }

        // Filter only .txt files
        const textFiles = files.filter(file => path.extname(file) === '.txt');
        res.json({ files: textFiles });
    });
});

app.listen(PORT, () => {
    console.log(`Server is running on port ${PORT}`);
});
