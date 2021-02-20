# Send pdfs to multiple users script

## Setup

```sh
# Get deps and build .js from .ts
$ yarn
$ yarn build
```

### Get the files you need

This is specific for the actual use-case of the program. From **gogift** half then job is getting the
pdfs downloaded from their `.csv`. This is not integrated in the program, but can be done with some
quick `shell` commands.

```sh
# The link to the pdf is the last column in each row
$ cat gogift.csv | awk -F "\"*;\"*" '{print $NF}' > links

# Create a new folder to hold pdfs
$ mkdir pdfs && cd pdfs

# Get all the links using wget
$ wget --no-check-certificate -E -H -k -K -p -e robots=off -nH --cut-dirs=3 -i ../links

# The files have no .pdf ending and have a long name so we can rename them
$ counter=0; for file in *; do [[ -f $file ]] && mv -i "$file" $((counter+1)).pdf && ((counter++)); done
```

The steps above will result in a folder called `pdfs` with all the pfds in them, named `n.pdf`

## Run

The script takes -3- command line args

`--from` or `F` = Organisation

`--occation` or `O` = Name of event this pdf is connected to

`--from_email` or `E` = fromEmail@domain.com>

`--pdfs` or `P` = path to folder with pdf files

`--emails` or `M` = path to file with emails

```sh
# Example (with pfds in ./input/pdfs and email file at /input/emails)
$ yarn start --from admin@abakus.no --pdfs input/pdfs --emails input/emails
```

## Check parsing and continue

The script will simple fold one email with one pdf

1. If `emails > pfds` it will throw an error
2. If `emails < pdfs` it will simple not use the rest of the pdfs

## Example

When starting the script it will output as shown below, and ask you to continue

`yes` or `Y` will start to send emails

```sh
❯ yarn start \
--from Abakus \
--occation Generalforsamling \
--from_email admin@abakus.no \
--pdfs input/pdfs \
--emails input/emails

yarn run v1.22.10
$ node build/index.js --from Abakus --occation Generalforsamling --from_email admin@abakus.no --pdfs input/pdfs --emails input/emails

=============================================
Finished parsing mails and pdfs
=============================================
Assigned pdfs as follows [
  { email: 'smith.peder@gmail.com', path: '1.pdf' },
  { email: 'peder@abakus.no', path: '2.pdf' },
  { email: 'reservekonto100@gmail.com', path: '3.pdf' }
]
prompt: continue:
```

Here you can see that everything is working as intended, and can track what email got what pdf.

```sh
=============================================
Next target: 	 smith.peder@gmail.com
Using file: 	 1.pdf
Email sent: 	 250 2.0.0 OK  1613814584 x36sm1196276lfu.129 - gsmtp
Envelope: 	 { from: 'admin@abakus.no', to: [ 'smith.peder@gmail.com' ] }
Accepted: 	 [ 'smith.peder@gmail.com' ]
=============================================
Next target: 	 peder@abakus.no
Using file: 	 2.pdf
Email sent: 	 250 2.0.0 OK  1613814586 e5sm1248442ljj.71 - gsmtp
Envelope: 	 { from: 'admin@abakus.no', to: [ 'peder@abakus.no' ] }
Accepted: 	 [ 'peder@abakus.no' ]
=============================================
Next target: 	 reservekonto100@gmail.com
Using file: 	 3.pdf
Email sent: 	 250 2.0.0 OK  1613814587 t14sm1249475ljc.70 - gsmtp
Envelope: 	 { from: 'admin@abakus.no', to: [ 'reservekonto100@gmail.com' ] }
Accepted: 	 [ 'reservekonto100@gmail.com' ]
✨  Done
```

![image](https://i.imgur.com/G5gNeYG.png)
