# Lexar NM790 Sleep Freeze Investigation  
### NVMe Maxio MAP1602 — зависания после пробуждения, 100% disk usage и Error Log 215k+

## Краткое описание проблемы
На ноутбуке Razer Blade 14 (2025) наблюдалось нестабильное поведение после пробуждения из Modern Standby (S0ix).  
Сразу после выхода из сна:

- система зависала на 5–30 секунд  
- загрузка диска подскакивала до 100%  
- проводник и консоль не отвечали  
- ощущалось сильное I/O-запирание  
- иногда происходили внезапные перезагрузки (Kernel-Power 41)

Проблема исчезала полностью после замены SSD.

## Конфигурация
- **SSD:** Lexar NM790 2TB  
- **Контроллер:** Maxio MAP1602  
- **Компьютер:** Razer Blade 14 (2025)  
- **Windows 11**  
- **Modern Standby включён**  

## SMART до замены

- Error Information Log Entries: 215298
- Media and Data Integrity Errors: 0
- Unsafe Shutdowns: 13
- Controller Busy Time: 109

## Анализ
### 1. Большое количество Error Log Entries (>200k)
Это не ошибка данных, но признак:

- повторяющихся I/O таймаутов  
- ошибок очередей NVMe  
- проблем с переходами в низкие энергосостояния (APST / ASPM)  
- некорректной работы контроллера MAP1602 после сна  

### 2. Характерно именно для Modern Standby  
Холодный старт → диск работает идеально.  
Сон → пробуждение → freeze.

### 3. Зависимости от прошивки  
Прошивка Lexar **не обновлялась**, и альтернатив от Maxio нет.

## Решение
После замены SSD на **Samsung 990 Pro 4TB**:

- нет зависаний  
- нет задержек после сна  
- 0 ошибок в Error Log  
- стабильное поведение APST / PCIe ASPM  

## Выводы
Это **совместимость MAP1602 + Modern Standby**, а не дефект конкретного экземпляра.  
Поведение воспроизводится и описывается другими пользователями NVMe на MAP1602.

Рекомендация:  
Для ноутбуков с Modern Standby лучше избегать NVMe на Maxio, пока производители не выпускают обновлённые прошивки.

<script src="https://utteranc.es/client.js"
        repo="ieya100/ieya100.github.io"
        issue-term="pathname"
        theme="github-light"
        crossorigin="anonymous"
        async>
</script>     
        
        
        
        
