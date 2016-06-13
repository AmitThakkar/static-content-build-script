# Static Content - Build Script

This is a build script repository which have a script for making zip files for static content and send mail with zip files.

```Script
#!/usr/bin/env bash
# Build Variables
OLD_VERSION="0.0.0"
NEW_VERSION="0.0.90"
CURRENT_PATH=${PWD}
PROJECTS=("Static Project1" "Static Project2" "Static Project3")
ENVIRONMENTS=("dev" "test" "both")

echo -e "\n\n"==============================Starting Build Process===========================================
echo "Please select a Project for making build: "

for projectIndex in "${!PROJECTS[@]}"
do
    echo $((${projectIndex} + 1)). ${PROJECTS[${projectIndex}]}
done

echo -n "Enter Project Number: "
read projectNumber

echo
case $projectNumber in
    1)
        sourcePath=/Users/amit/Data/Projects/CP/StaticProject1
        destinationPath=~/Desktop/build/sp1
        configFolder=~/Desktop/config/sp1
        prefixForZipFile=sp1_
        ;;
    2)
        sourcePath=/Users/amit/Data/Projects/CP/StaticProject2
        destinationPath=~/Desktop/build/sp2
        configFolder=~/Desktop/config/sp2
        prefixForZipFile=sp2_
        ;;
    3)
        sourcePath=/Users/amit/Data/Projects/StaticProject3
        destinationPath=~/Desktop/build/sp3
        configFolder=~/Desktop/config/sp3
        prefixForZipFile=sp3_
        ;;
esac

#Mail Variables
SUBJECT="${PROJECTS[${projectNumber}-1]} build for"
RECEIVER="vigildbest@gmail.com amit.thakkar01@outlook.com"
BODY="Please find attachment."
ATTACHMENTS=""

echo "Do you want make build for environment:"
for environmentIndex in "${!ENVIRONMENTS[@]}"
do
    echo $((${environmentIndex} + 1)). ${ENVIRONMENTS[${environmentIndex-1}]}
done

echo -n "Enter Environment Number: "
read environmentNumber

echo -e "\nMaking build for project: ${PROJECTS[${projectNumber}-1]} in ${ENVIRONMENTS[${environmentNumber}-1]} Envonment."

cd ${sourcePath}
svn update

case ${environmentNumber} in
    1)  MakeBuildForEnvironment ${ENVIRONMENTS[0]}
        SUBJECT="${SUBJECT} ${ENVIRONMENTS[0]} Environment"
        ;;
    2)  MakeBuildForEnvironment ${ENVIRONMENTS[1]}
        SUBJECT="${SUBJECT} ${ENVIRONMENTS[1]} Environemnt"
        ;;
    3)  MakeBuildForEnvironment ${ENVIRONMENTS[0]}
        MakeBuildForEnvironment ${ENVIRONMENTS[1]}
        SUBJECT="${SUBJECT} ${ENVIRONMENTS[0]}, ${ENVIRONMENTS[1]} Environments"
        ;;
esac

MakeBuildForEnvironment() {
    rm -rf ${destinationPath}/${1}/*
    cp -r  ${sourcePath}/public/ ${destinationPath}/${1}/public
    cp -r  ${sourcePath}/index.html ${destinationPath}/${1}
    cp -r  ${sourcePath}/oos.html ${destinationPath}/${1}
    cp ${configFolder}/${1}/config.xml ${destinationPath}/${1}/cda_resources/
    sed "s/${OLD_VERSION}/${NEW_VERSION}/" < ${configFolder}/${1}/config.xml > ${destinationPath}/${1}/public/config.xml
    cd ${destinationPath}/${1}/
    zip -r ${destinationPath}/${1}/${prefixForZipFile}${1} *
    ATTACHMENTS=" $ATTACHMENTS ${destinationPath}/${1}/${prefixForZipFile}${1}.zip"
}

#Move to Current Path
cd ${CURRENT_PATH}

echo -n "Do you want to send mail with attachments? [y/N]:"
read sendMailFlag

if [ "${sendMailFlag}" ] && [ "${sendMailFlag}" != "" ] && [ "N" != "${sendMailFlag}" ] && [ "n" != "${sendMailFlag}" ]; then
	SendMail
fi

SendMail() {
    #Sending Mail
    echo -e "\n\n\nSending Mail(${SUBJECT}) to ${RECEIVER}"
    echo ${BODY} | mutt -a ${ATTACHMENTS} -s "${SUBJECT}" -- ${RECEIVER}
    echo "Mail Sent"
}
```

Follow Me
---
[Github](https://github.com/AmitThakkar)

[Twitter](https://twitter.com/amit_thakkar01)

[LinkedIn](https://in.linkedin.com/in/amitthakkar01)

[More Blogs By Me](http://amitthakkar.github.io/)