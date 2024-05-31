
# Inline Autocomplete Example

Bu proje, bir `textarea` içinde yazarken kelime önerileri sunar ve önerilen kelimeyi gri renkte gösterir. Kullanıcı `Tab` tuşuna bastığında önerilen kelime otomatik olarak tamamlanır.

## Kullanım Talimatları

### Önerileri Girme

Üstteki metin kutusuna öneri kelimelerini virgülle ayırarak girin. Örneğin:
```
merhaba, nasılsın
```

### Yazma ve Öneri Görme

Alttaki metin kutusuna yazarken öneriler gri renkte görünecektir.

### Öneriyi Kabul Etme

Öneriyi kabul etmek için `Tab` tuşuna basın. Önerilen kelime otomatik olarak tamamlanacaktır.

## Kod Açıklamaları

### HTML ve CSS

HTML yapısı iki `textarea` içerir. İlk `textarea` öneri kelimelerinin girilmesi içindir. İkinci `textarea` kullanıcı metin yazarken önerileri göstermek içindir.

```html
<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Inline Autocomplete Example</title>
  <style>
    body {
      font-family: 'Arial', sans-serif;
      background-color: #1e1e1e;
      color: #eaeaea;
      display: flex;
      flex-direction: column;
      align-items: center;
      height: 100vh;
      margin: 0;
      padding: 20px;
    }
    #autocomplete-container {
      position: relative;
      width: 500px;
      margin-top: 20px;
    }
    #autocomplete-textarea, #suggestion-input {
      width: 100%;
      padding: 10px;
      font-size: 18px;
      border: 2px solid #444;
      border-radius: 5px;
      background-color: #2b2b2b;
      color: #eaeaea;
      position: relative;
      z-index: 1;
      margin-bottom: 10px;
    }
    #autocomplete-textarea {
      height: 150px;
      resize: none;
      line-height: 1.5;
    }
    #autocomplete-suggestion {
      position: absolute;
      top: 10px;
      left: 10px;
      font-size: 18px;
      color: #555;
      z-index: 0;
      pointer-events: none;
      white-space: pre-wrap;
      word-wrap: break-word;
      width: calc(100% - 20px);
      overflow: hidden;
    }
    textarea:focus, input:focus {
      outline: none;
      border-color: #03aed2;
    }
    .instructions {
      color: #aaa;
      margin-bottom: 20px;
    }
  </style>
</head>
<body>
  <div class="instructions">
    Önerileri üstteki metin kutusuna virgülle ayırarak girin (örn: merhaba, nasılsın). <br>
    Alttaki metin kutusuna yazarken öneriler gri renkte görünecek. <br>
    Öneriyi kabul etmek için Tab tuşuna basın.
  </div>
  <div>
    <textarea id="suggestion-input" placeholder="Önerileri girin, örn: merhaba, nasılsın"></textarea>
  </div>
  <div id="autocomplete-container">
    <textarea id="autocomplete-textarea" placeholder="Bir şeyler yazın..." autocomplete="off"></textarea>
    <div id="autocomplete-suggestion"></div>
  </div>
</body>
</html>
```

### JavaScript

JavaScript kodu, öneri kelimelerini günceller ve kullanıcı yazarken önerileri gösterir. `Tab` tuşuna basıldığında önerilen kelime tamamlanır.

```javascript
<script>
  let suggestions = [];
  const suggestionInput = document.getElementById('suggestion-input');
  const textarea = document.getElementById('autocomplete-textarea');
  const suggestionBox = document.getElementById('autocomplete-suggestion');

  // Önerileri girdiğinizde öneri listesini günceller
  suggestionInput.addEventListener('input', function() {
    suggestions = this.value.split(',').map(s => s.trim().toLowerCase());
    updateSuggestion();
  });

  // Textarea'ya yazarken öneriyi günceller
  textarea.addEventListener('input', function() {
    updateSuggestion();
  });

  // Tab tuşuna basıldığında öneriyi tamamlar
  textarea.addEventListener('keydown', function(e) {
    if (e.key === 'Tab') {
      e.preventDefault();
      const cursorPosition = textarea.selectionStart;
      const textBeforeCursor = textarea.value.substring(0, cursorPosition);
      const words = textBeforeCursor.split(/[\s
]/);
      const lastWord = words[words.length - 1].toLowerCase();
      const match = getLongestMatch(lastWord);

      if (lastWord && match) {
        words[words.length - 1] = match;
        const newValue = words.join(' ') + textarea.value.substring(cursorPosition);
        textarea.value = newValue;
        textarea.selectionStart = textarea.selectionEnd = cursorPosition + (match.length - lastWord.length);
        updateSuggestion();
      }
    }
  });

  // Textarea'dan çıktığınızda öneriyi temizler
  textarea.addEventListener('blur', function() {
    suggestionBox.textContent = this.value;
  });

  // Öneriyi güncelleyen fonksiyon
  function updateSuggestion() {
    const value = textarea.value;
    const cursorPosition = textarea.selectionStart;
    const textBeforeCursor = value.substring(0, cursorPosition);
    const words = textBeforeCursor.split(/[\s
]/);
    const lastWord = words[words.length - 1].toLowerCase();
    const match = getLongestMatch(lastWord);

    if (lastWord && match) {
      const suggestion = match.slice(lastWord.length);
      suggestionBox.textContent = textBeforeCursor + suggestion + value.substring(cursorPosition);
    } else {
      suggestionBox.textContent = value;
    }
  }

  // En uzun eşleşmeyi bulan fonksiyon
  function getLongestMatch(word) {
    return suggestions.filter(suggestion => suggestion.startsWith(word))
                      .reduce((longest, current) => current.length > longest.length ? current : longest, '');
  }
</script>
```

## Lisans

Bu proje MIT Lisansı ile lisanslanmıştır. Ayrıntılar için `LICENSE` dosyasına bakın.
