# Inverse Text Normalization

## About
<p>Inverse text normalization (ITN) is a part of the Automatic Speech Recognition (ASR) post-processing pipeline. ITN is the task of converting the raw spoken output of the ASR model into its written form to improve text readability. We currently only handle numbers as a part of our ITN pipeline, and have developed and open-sourced WFST (weighted finite state transducer) based ITN support for 11 Indic languages -Hindi, Gujarati, Telugu, Marathi, Punjabi, Tamil, Bengali, Malayalam, Odia, Assamese, Kannada, using NVIDIA’s NEMO toolkit. </p>

## Installation Instructions 

```buildoutcfg
git clone https://github.com/Open-Speech-EkStep/indic-punct.git
cd indic-punct
bash install.sh
python setup.py bdist_wheel
pip install -e .
```

## Usage

Inverse Text Normalization:
- Hindi
- English
- Gujarati
- Telugu
- Marathi
- Punjabi
- Tamil
- Bengali
- Malayalam
- Odia
- Assamese
- Kannada



We are planning to add other Indic languages. 

### Inverse Text Normalization
```python
from inverse_text_normalization.run_predict import inverse_normalize_text
inverse_normalize_text(['I have twenty cars',
                        'The army had four thousand six hundred forty six horses'],
                         lang='en')
inverse_normalize_text(['दस लाख एक हज़ार चार सौ बीस', 'चार करोड़ चार लाख'], lang='hi')
inverse_normalize_text(['મારી પાસે ત્રણ બિલાડીઓ છે', 'ચાર કરોડ ચાર લાખ', 'તેને એક હજાર ચારસો ચાર રૂપિયા આપો'], lang='gu')
inverse_normalize_text(['ఏడు లక్షల నాలుగు వేల తొమ్మిది వందల యాభై ఒకటి', 'నేను ఏడు వందల పదమూడు సినిమాలు చూశాను'], lang='te')
inverse_normalize_text(['रीटाकडे नऊशे वीस मांजरी आहेत','बत्तीस कोटी एकवीस लाख सदतीस हजार चारशे बारा'], lang='mr')
inverse_normalize_text(['ਬਾਰਾਂ ਲੱਖ ਵੀਹ ਹਜਾਰ ਸੱਤ ਸੌ ਪੰਦਰਾਂ','ਮੇਰੇ ਕੋਲ ਦਸ ਰੁਪਏ ਹਨ'], lang='pa')
inverse_normalize_text(['ஒன்று நூறு முப்பத்து ஒன்பது படங்கள் பார்த்திருக்கிறேன்','தொண்ணூற்றிநான்கு கோடி ஐந்து இலட்சம் முந்நூறு இருபத்து இரண்டு'], lang='ta')
inverse_normalize_text(['আমার পাঁচটি কলম আছে', 'তিনি দুইশত সাতটি সিনেমা দেখেছেন'], lang='bn')
inverse_normalize_text(['ഇരുനൂറ്റി അമ്പത് രൂപ ഞാൻ അവന് കൊടുത്തു', 'അവൻ എനിക്ക് പത്ത് യൂറോ തന്നു'], lang='ml')
inverse_normalize_text(['ମୋ ହାତରେ ପାଞ୍ଚ ଡଲାର ଅଛି', 'ମୋ ହାତରେ ପାଞ୍ଚ ଶହ ଟଙ୍କା ଅଛି', 'ମୋ ହାତରେ ସାତ ଶହ ୟୁରୋ ଅଛି'], lang='or')
inverse_normalize_text(['মই 10 বাকচ মিঠাই বিতৰণ কৰিলো', 'নিৰান্নব্বৈটা কোটি পাঁচ লাখ আঠশ বাইছ'], lang='as')
inverse_normalize_text(['ನನ್ನ ಕೈಯಲ್ಲಿ ಐದು ಡಾಲರ್ ಇದೆ', 'ನನ್ನ ಬ್ಯಾಗ್ ನಲ್ಲಿ ಐದು ನೂರು ರೂಪಾಯಿ ಪೆನ್ನಿದೆ', 'ನನ್ನ ಖಾತೆಯಲ್ಲಿ ಐದು ಕೋಟಿ ಯೂರೋ ಇದೆ'], lang='kn')

----Outputs----
['I have 20 cars', 'The army had 4646 horses']
['10,01,420', '4,04,00,000']    
['મારી પાસે 3 બિલાડીઓ છે', '4,04,00,000', 'તેને ₹ 1,404 આપો']
['7,04,951', 'నేను 713 సినిమాలు చూశాను']
['रीटाकडे 920 मांजरी आहेत','32,21,37,412']
['12,20,715', 'ਮੇਰੇ ਕੋਲ ₹ 10 ਹਨ']
['139 படங்கள் பார்த்திருக்கிறேன்', '94,05,00,322']
['আমার 5 কলম আছে', 'তিনি 207 সিনেমা দেখেছেন']
['₹ 250 ഞാൻ അവന് കൊടുത്തു', 'അവൻ എനിക്ക് € 10 തന്നു']
['ମୋ ହାତରେ $ 5 ଅଛି', 'ମୋ ହାତରେ ₹ 500 ଅଛି', 'ମୋ ହାତରେ € 700 ଅଛି']
['মই 10 বাকচ মিঠাই বিতৰণ কৰিলো', '99,05,00,822']
['ನನ್ನ ಕೈಯಲ್ಲಿ $ 5 ಇದೆ', 'ನನ್ನ ಬ್ಯಾಗ್ ನಲ್ಲಿ ₹ 500 ಪೆನ್ನಿದೆ', 'ನನ್ನ ಖಾತೆಯಲ್ಲಿ € 5,00,00,000 ಇದೆ']
```
