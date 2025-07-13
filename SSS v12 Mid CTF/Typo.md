# Typo

We visited **/robots.txt** where we found:

```html
# Group 1
User-agent: Googlebot
Disallow: /history/aeschylus.jpg
Disallow: /history/agrippa.jpg
Disallow: /history/akhenaten.jpg
Disallow: /history/alaric-the-visigoth.jpg
Disallow: /history/alexander-the-great.jpg
Disallow: /history/amenhotep-iii.jpg
Disallow: /history/anaximander.jpg
Disallow: /history/anaximenes.jpg
Disallow: /history/archimedes.jpg
Disallow: /history/aristophanes.jpg
Disallow: /history/aristotle.jpg
Disallow: /history/ashoka.jpg
Disallow: /history/attila-the-hun.jpg
Disallow: /history/augustine-of-hippo.jpg
Disallow: /history/augustus-octavian.jpg
Disallow: /history/boudicca.jpg
Disallow: /history/caligula.jpg
Disallow: /history/cato-the-elder.jpg
Disallow: /history/catullus.jpg
Disallow: /history/chin-the-first-emperor.jpg
Disallow: /history/cicero.jpg
Disallow: /history/cleopatra.jpg
Disallow: /history/confucius.jpg
Disallow: /history/constantine-the-great.jpg
Disallow: /history/cyrus-the-great.jpg
Disallow: /history/darius-the-great.jpg
Disallow: /history/demosthenes.jpg
Disallow: /history/domitian.jpg
Disallow: /history/empedocles.jpg
Disallow: /history/eratosthenes.jpg
Disallow: /history/euclid.jpg
Disallow: /history/euripides.jpg
Disallow: /history/galen.jpg
Disallow: /history/hammurabi.jpg
Disallow: /history/hannibal.jpg
Disallow: /history/hatshepsut.jpg
Disallow: /history/heraclitus.jpg
Disallow: /history/herodotus.jpg
Disallow: /history/hippocrates.jpg
Disallow: /history/homer.jpg
Disallow: /history/imhotep.jpg
Disallow: /history/jesus.jpg
Disallow: /history/julius-caesar.jpg
Disallow: /history/justinian-the-great.jpg
Disallow: /history/lucretius.jpg
Disallow: /history/mithridates-mithradates-of-pontus.jpg
Disallow: /history/moses.jpg
Disallow: /history/nebuchadnezar-ii.jpg
Disallow: /history/nefertiti.jpg
Disallow: /history/nero.jpg
Disallow: /history/ovid.jpg
Disallow: /history/parmenides.jpg
Disallow: /history/paul-of-tarsus.jpg
Disallow: /history/pericles.jpg
Disallow: /history/pindar.jpg
Disallow: /history/plato.jpg
Disallow: /history/plutarch.jpg
Disallow: /history/ramses.jpg
Disallow: /history/sappho.jpg
Disallow: /history/sargon-the-great-of-akkad.jpg
Disallow: /history/scipio-africanus.jpg
Disallow: /history/seneca.jpg
Disallow: /history/siddhartha-gautama-buddha.jpg
Disallow: /history/socrates.jpg
Disallow: /history/solon.jpg
Disallow: /history/sophocles.jpg
Disallow: /history/spartacus.jpg
Disallow: /history/tacitus.jpg
Disallow: /history/thales.jpg
Disallow: /history/themistocles.jpg
Disallow: /history/thucydides.jpg
Disallow: /history/trajan.jpg
Disallow: /history/vergil-virgil.jpg
Disallow: /history/xerxes-the-great.jpg
Disallow: /history/zoroaster.jpg

# Group 2
User-agent: *
Allow: /
```

We looked through the files and first noticed a typo in *chin* might be *qin*, but this was not the typo we needed. The correct one was */history/nebuchadnezar-ii.jpg* it should be */history/nebuchadnezzar-ii.jpg*. 

We downloaded the image and ran an exif command on it that revealed the metadata:

```
BitsPerSample	8
ColorComponents	3
Comment	SSS{O_god_Nabu_defend_my_firstborn_son}
EncodingProcess	Progressive DCT, Huffman coding
FileAccessDate	2025-07-13 18:39:08 +0200
FileInodeChangeDate	2025-07-13 18:39:08 +0200
FileModifyDate	2025-07-13 18:39:08 +0200
FilePermissions	prw-------
FileSize	0 bytes
FileType	JPEG
FileTypeExtension	jpg
ImageHeight	357
ImageSize	353x357
ImageWidth	353
JFIFVersion	1.01
MIMEType	image/jpeg
Megapixels	0.126
ResolutionUnit	None
XResolution	1
YCbCrSubSampling	YCbCr4:2:0 (2 2)
YResolution	1
```

In the comment section we found the flag: **Comment**	***SSS{O_god_Nabu_defend_my_firstborn_son}***
