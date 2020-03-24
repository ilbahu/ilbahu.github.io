---
layout: post
title: STDIO Veri Akışı
# subtitle:
blog: true
categories: [linux]
tags: [stdio]
# level:
# image: /img/hello_world.jpeg
# bigimg: /img/path.jpg
# tags: [books, shakespeare, test]
# gh-repo:
# gh-badge: [star, fork, follow]
comments: false
---


Standard Input/Output (STDIO) Linux'un bir şeyler yapabilmesi için kilit bir rol oynamaktadır.

> This is the Unix philosophy: Write programs that do one thing and do it well. Write programs to work together. Write programs to handle text streams, because that is a universal interface.
— Doug McIlroy, Basics of the Unix Philosophy

STDIO bir programın, dosyanın veya cihazın çıktısından başka bir programın, dosyanın veya cihazın girişine veri akışı yapmaktır.
Üç tane STDIO veri akışı vardır.

  **STDIN** - 0 - Genellikle klavyeden girilen standart girdidir. Klavye yerine aygıt dosyaları da dahil olmak üzere herhangi bir dosyadan yeniden yönlendirilebilir.

  **STDOUT** - 1 - Veri akışını ekrana gönderen standart çıktıdır. STDOUT'u bir dosyaya yönlendirmek veya başka programa yönlendirilebilir.

  **STDERR** - 2 - veri akışı genelde ekrana gönderilebilir. İstendiği zaman yönlendirilebilir.

`yes merhaba` CTRL+C tuşuna basılarak terminate edilene kadar girilen string dizinini veya 'y' karakterini ekrana yazdırır.
`yes | rm *`

Sanal makine üzerinde 2 GB'lık bir partition oluşturdum. bu bölüm üzerinde
`yes merhaba > test.txt`
disk üzerinde yer kalmayınca kadar işlem devam ediyor.
`df -h /dev/sdc1`

### Yönlendirme

`for i in {0..10}; do echo "This is file$i"> file$i.txt;done`

`cat file0.txt file1.txt file2.txt> test.txt`

`cat test.txt`

`cat file0.txt file1.txt file23.txt> test.txt`

`cat test.txt`

"cat: file23.txt: No such file or directory" dosyayı bulamadığı için STDERR ekrana hatayı yazdırır. Çıktıları yönlendirmek için aşağıdaki işlemler yapılabilir.

STDOUT ve STDERR dosyaya yönelendirmek için:

`cat file0.txt file1.txt file23.txt &>test1.txt`

STDOUT dosyaya yönelendirmek için:

`cat file0.txt file1.txt file23.txt 1>test1.txt`

STDERR dosyaya yönlendirmek için: (STDOUT ekrana yazdırılır):

`cat file0.txt file1.txt file23.txt 2>test1.txt`

Farklı dosyalara yönlendirmek için:

`cat file0.txt file1.txt file23.txt 2> error.txt 1> good.txt`

Çıktıların bizim için bir önemi yoksa `/dev/null` yönlendirilebilir.
