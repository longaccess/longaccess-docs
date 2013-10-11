The latest version of this document is always available at [github.com/longaccess/longaccess-docs](http://github.com/longaccess/longaccess-docs)

#What is Longaccess?

Longaccess is a long-term data storage. 

You buy one or more storage spaces (there are various storage sizes available) called **DataCapsule**, and we keep its contents for 30 years. You group and upload your data in bunches we call **archives**. Each archive is encrypted and the ONLY way to download the data and decrypt it is to use the **certificate** you get when the upload is completed. You can't delete archives -however, you can render them useless, by destroying the associated certificate.

## How it works - Overview.

1. You create an account at longaccess.com, and buy (or get for free...) a DataCapsule.
2. You download the longaccess application
3. You run the application on your computer and select the files you want to upload.
4. Your files are packaged and encrypted in an Archive. 
5. The Archive is uploaded to your DataCapsule.
6. The longaccess application generates a Certificate: a small text file, that is needed to download and decrypt your Archive in the future.
7. Just keep the Certificate somewhere safe. Your data will be preserved for 30 years and you can use the Certificate to download them.
8. You can print a copy of the certificate to share with your family, your kids, your lawyer, your friends. Anyone holding a copy of the Certificate can download the data.

This is how a Certificate will look like:

    Visit www.longaccess.com for instructions.
    
    Archive ID: 123456-AGP9
    Key: 9F A2 50 09 AD 20 C5 26 . 23 50 97 24 22 FD AE 1A
         0F CC 61 26 BB A0 1B 5C . 6C 13 6D A0 1C 76 24 33 
    
    Uploaded by:    Jane Doe <jane@example.com>
    Upload date:    2013-10-24
    Notes:          A year around the world photos and videos. 2010-2011. 
                    Best trip ever!

    Checksum:       C5 4E 45 88 47 13 2B 04 . 96 45 5D 98 5A C7 CD BA

## How it works - In detail.

### Data Capsules

User data are stored in *Data Capsules*. Each DataCapsule has a *size*, a *Start Date* and an *Expiration Date*.

Users buy "DataCapsules". A DataCapsule is a reserved space in our back end. It has three characteristics:

- **Size:** DataCapsules of 250MB, 1GB, 10GB and 100GB will be available soon. A user may buy one or more DataCapsules, of any size. During our test period, the only size available is 250MB.
- **Start Date:** The *Start Date* of a DataCapsule is the date it is purchased.
- **Expiration Date:** All DataCapsules have a life span of thirty years. The *Expiration Date* is thirty years after the *Start Date*.

Example: If you buy a 1GB Data Capsule on December 3, 2012, its *Start Date* will be 3-Dec-2013 and its *Expiration Date* will be 3-Dec-2043.

DataCapsules are what we call "cold storage". Much like a home freezer, they can preserve data for very long periods of time, but when you need it, you will have to wait a couple of hours for our servers to "defrost" it before it's available for you.

### Archives

If you think of a DataCapsule as your own special storage space, you can think of Archives as small, extremely secure and sealed safety boxes: 

- You select the data you want to preserve
- Our application creates a ZIP containing your files, then it encrypts this ZIP using AES256 and a randomly generated key.
- The Archive is uploaded to one of your DataCapsules. 
- Once the upload is complete and verified, the longaccess application will generate a *Certificate*. This is a simple text file you can save on your computer or even better print on real paper. More about this, later.

What you should know about Archives:

1. an Archive can't be deleted.
2. an Archives can't be updated.
3. an Archive can be downloaded and decrypted **only if you have the certificate**.
4. **anyone** with a copy of the certificate can download and decrypt the data.

### Certificates

After successfully uploading an Archive, the longaccess application generates a *Certificate*. This is a simple text file containing essential information required to retrieve your data. The most important information contained in the Certificate is

- the *Archive ID*, a unique number identifying your archive 
- the encryption/decryption *Key*, a long series of number and letters.

You can save the Certificate on your hard disk. However, we **strongly advise** that you print it and keep it safe. Depending on the nature of data contained in the specific Archive, it would also be wise to give a copy to people you trust (like family or friends). 

There may be cases where the data you uploaded are also of interest to others. In these cases, you could also give a copy of the Certificate to them. For example, if you upload the photo and video archive of a wedding, you could give a printed copy of the certificate to the married couple, the best man and the families of the newlywed.