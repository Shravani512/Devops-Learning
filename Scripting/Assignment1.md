### Assignment1: write a script to created folder and file
```
#!/bin/bash

echo "Creating a folder"

mkdir -p rancho

foldername=$(find . -type d -name "rancho" -printf "%f")

if find . -type d -name "rancho" | grep -q .; then
        echo "$foldername folder created sucessfully"
else
        echo "$foldername folder not created"
fi

echo "Creating a file"

touch assignment.txt

filename=$(find . -type f -name "assignment.txt" -printf "%f")

if find . -type f -name "assignment.txt" | grep -q .; then
        echo "$filename File created sucessfully"
else
        echo "$filename file not created"
fi
```
