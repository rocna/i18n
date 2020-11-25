## Sınıf: TouchBar

> Yerel macOS uygulamaları için TouchBar düzenlemeleri oluşturun

Süreç: [Ana](../glossary.md#main-process)

### `new TouchBar(options)`

* `options` Object
  * `items` ([TouchBarButton](touch-bar-button.md) | [TouchBarColorPicker](touch-bar-color-picker.md) | [TouchBarGroup](touch-bar-group.md) | [TouchBarLabel](touch-bar-label.md) | [TouchBarPopover](touch-bar-popover.md) | [TouchBarScrubber](touch-bar-scrubber.md) | [TouchBarSegmentedControl](touch-bar-segmented-control.md) | [TouchBarSlider](touch-bar-slider.md) | [TouchBarSpacer](touch-bar-spacer.md))[] (optional)
  * `escapeItem` ([TouchBarButton](touch-bar-button.md) | [TouchBarColorPicker](touch-bar-color-picker.md) | [TouchBarGroup](touch-bar-group.md) | [TouchBarLabel](touch-bar-label.md) | [TouchBarPopover](touch-bar-popover.md) | [TouchBarScrubber](touch-bar-scrubber.md) | [TouchBarSegmentedControl](touch-bar-segmented-control.md) | [TouchBarSlider](touch-bar-slider.md) | [TouchBarSpacer](touch-bar-spacer.md) | null) (isteğe bağlı)

Creates a new touch bar with the specified items. Use `BrowserWindow.setTouchBar` to add the `TouchBar` to a window.

**Note:** The TouchBar API is currently experimental and may change or be removed in future Electron releases.

**Tip:** If you don't have a MacBook with Touch Bar, you can use [Touch Bar Simulator](https://github.com/sindresorhus/touch-bar-simulator) to test Touch Bar usage in your app.

### Static Properties

#### `TouchBarButton`

A [`typeof TouchBarButton`](./touch-bar-button.md) reference to the `TouchBarButton` class.

#### `TouchBarColorPicker`

A [`typeof TouchBarColorPicker`](./touch-bar-color-picker.md) reference to the `TouchBarColorPicker` class.

#### `TouchBarGroup`

A [`typeof TouchBarGroup`](./touch-bar-group.md) reference to the `TouchBarGroup` class.

#### `TouchBarLabel`

A [`typeof TouchBarLabel`](./touch-bar-label.md) reference to the `TouchBarLabel` class.

#### `TouchBarPopover`

A [`typeof TouchBarPopover`](./touch-bar-popover.md) reference to the `TouchBarPopover` class.

#### `TouchBarScrubber`

A [`typeof TouchBarScrubber`](./touch-bar-scrubber.md) reference to the `TouchBarScrubber` class.

#### `TouchBarSegmentedControl`

A [`typeof TouchBarSegmentedControl`](./touch-bar-segmented-control.md) reference to the `TouchBarSegmentedControl` class.

#### `TouchBarSlider`

A [`typeof TouchBarSlider`](./touch-bar-slider.md) reference to the `TouchBarSlider` class.

#### `TouchBarSpacer`

A [`typeof TouchBarSpacer`](./touch-bar-spacer.md) reference to the `TouchBarSpacer` class.

#### `TouchBarOtherItemsProxy`

A [`typeof TouchBarOtherItemsProxy`](./touch-bar-other-items-proxy.md) reference to the `TouchBarOtherItemsProxy` class.

### Örnek özellikleri

Aşağıdaki özellikler `TouchBar` örneklerinde mevcuttur:

#### `touchBar.escapeItem`

Ayarlandığında touch bar üzerindeki "esc" butonun işlevini görecek bir `TouchBarItem`. `null` olarak ayarlamak varsayılan "esc" düğmesini geri yükler. Bu değeri değiştirmek, touch bar üzerindeki öğeyi hemen günceller.

## Örnekler

Aşağıda düğme ve bazı etiketler içeren basit bir slot makinesi touch bar oyunu örnek olarak verilmiştir.

```javascript
const { app, BrowserWindow, TouchBar } = require('electron')

const { TouchBarLabel, TouchBarButton, TouchBarSpacer } = TouchBar

let spinning = false

// Reel labels
const reel1 = new TouchBarLabel()
const reel2 = new TouchBarLabel()
const reel3 = new TouchBarLabel()

// Spin result label
const result = new TouchBarLabel()

// Spin button
const spin = new TouchBarButton({
  label: '🎰 Spin',
  backgroundColor: '#7851A9',
  click: () => {
    // Ignore clicks if already spinning
    if (spinning) {
      return
    }

    spinning = true
    result.label = ''

    let timeout = 10
    const spinLength = 4 * 1000 // 4 seconds
    const startTime = Date.now()

    const spinReels = () => {
      updateReels()

      if ((Date.now() - startTime) >= spinLength) {
        finishSpin()
      } else {
        // Slow down a bit on each spin
        timeout *= 1.1
        setTimeout(spinReels, timeout)
      }
    }

    spinReels()
  }
})

const getRandomValue = () => {
  const values = ['🍒', '💎', '7️⃣', '🍊', '🔔', '⭐', '🍇', '🍀']
  return values[Math.floor(Math.random() * values.length)]
}

const updateReels = () => {
  reel1.label = getRandomValue()
  reel2.label = getRandomValue()
  reel3.label = getRandomValue()
}

const finishSpin = () => {
  const uniqueValues = new Set([reel1.label, reel2.label, reel3.label]).size
  if (uniqueValues === 1) {
    // All 3 values are the same
    result.label = '💰 Jackpot!'
    result.textColor = '#FDFF00'
  } else if (uniqueValues === 2) {
    // 2 values are the same
    result.label = '😍 Winner!'
    result.textColor = '#FDFF00'
  } else {
    // No values are the same
    result.label = '🙁 Spin Again'
    result.textColor = null
  }
  spinning = false
}

const touchBar = new TouchBar({
  items: [
    spin,
    new TouchBarSpacer({ size: 'large' }),
    reel1,
    new TouchBarSpacer({ size: 'small' }),
    reel2,
    new TouchBarSpacer({ size: 'small' }),
    reel3,
    new TouchBarSpacer({ size: 'large' }),
    result
  ]
})

let window

app.whenReady().then(() => {
  window = new BrowserWindow({
    frame: false,
    titleBarStyle: 'hiddenInset',
    width: 200,
    height: 200,
    backgroundColor: '#000'
  })
  window.loadURL('about:blank')
  window.setTouchBar(touchBar)
})
```

### Yukarıdaki örneğin çalıştırılması

Yukarıdaki örneği çalıştırmak için, (örneği çalıştırmak istediğiniz dizinde bir terminalin açık olduğunu varsayıyoruz):

1. Yukarıdaki dosyayı `touchbar.js` olarak bilgisayarınıza kaydedin
2. `npm install electron` aracılığıyla Electron'u yükleyin
3. Örneği Electron'da çalıştırın: `./node_modules/.bin/electron touchbar.js`

Ardından yeni bir Electron penceresi ve uygulamanızın dokunmatik çubuğunuzda (veya dokunmatik çubuklu emülatörünüzde) çalıştığını görmeniz gerekir.