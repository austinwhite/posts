#!/usr/bin/env bash

if [ "$#" -gt 1 ]; then
    echo "error: to many arguments"
    exit 1
fi

if [ "$#" -lt 1 ]; then
    echo "error: must supply post name"
    exit 1
fi

NAME=`echo $1 | sed -e 's/ /_/g'`
MD_FILE=$NAME/$NAME.md

mkdir -p $NAME/images

echo "created directory: $NAME"
echo "created directory: $NAME/images"

echo "---" >> $MD_FILE
echo "title: title" >> $MD_FILE
echo "tags:" >> $MD_FILE
echo "  - tag" >> $MD_FILE
echo "date: MM-DD-YYYY" >> $MD_FILE
echo "summary: summary" >> $MD_FILE
echo "---" >> $MD_FILE

echo "created file: $NAME/$NAME.md"