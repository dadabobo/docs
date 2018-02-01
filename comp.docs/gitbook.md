## GitBook
* [GitBook.com](https://www.gitbook.com/)
* [GitbookIO/gitbook](https://github.com/GitbookIO/gitbook)
* [GitBook Toolchain Documentation](https://toolchain.gitbook.com/)
* [GitBook setup](https://github.com/GitbookIO/gitbook/blob/master/docs/setup.md)


#### GitBook.com
[GitBook.com](https://www.gitbook.com/) is an easy to use solution to write, publish and host books. It is the easiest solution for publishing your content and collaborating on it.
It integrates well with the [GitBook Editor](https://www.gitbook.com/editor).


#### Local Installation
`npm install gitbook-cli -g`

```
mkdir mybook && cd mybook
gitbook init
gitbook serve
gitbook build
gitbook pdf
gitbook epub ./ mybook.epub
```

Installing ebook-convert  
`ebook-convert` is required to generate ebooks (epub, mobi, pdf).  
`sudo apt install calibre`


#### Directory Structure
```
.
├── book.json
├── README.md
├── SUMMARY.md
├── chapter-1/
|   ├── README.md
|   └── something.md
└── chapter-2/
    ├── README.md
    └── something.md
```

book.json
```json
{
  "root": "./firstbook",
  "plugins": ["mermaid-atom-mden"]
}
```

```
book.json
firstbook/README.md
firstbook/SUMMARY.md
```
