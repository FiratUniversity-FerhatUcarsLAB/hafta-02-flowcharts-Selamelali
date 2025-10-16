ATM Para Çekme Sistemi
// Veri yapıları
Account:
  id
  pin
  balance         // bakiye (para birimi)
  dailyWithdrawn  // bugün çekilmiş toplam
  dailyLimit      // günlük çekim limiti
  status          // "active", "blocked", vb.

ATM:
  cashInventory   // {denomination: count, ...} örn {200:10,100:20,50:30,20:50,10:100}
  totalCash() => sum(denomination * count for each)
  status          // "online", "offline", "maintenance"

// Kayıt/log
TransactionLog.append(entry)

// Yardımcı fonksiyonlar
function authenticate(card, enteredPIN):
  account = getAccountFromCard(card)
  if account == null: return (false, "Hesap bulunamadı")
  if account.status != "active": return (false, "Hesap bloklu")
  if enteredPIN != account.pin:
    account.failedAttempts += 1
    if account.failedAttempts >= 3:
      account.status = "blocked"
      return (false, "PIN yanlış. Hesap bloklandı.")
    return (false, "PIN yanlış.")
  account.failedAttempts = 0
  return (true, account)

function checkDailyLimit(account, amount):
  if amount + account.dailyWithdrawn > account.dailyLimit:
    return (false, "Günlük limit aşılıyor")
  return (true, "")

function checkBalance(account, amount):
  if amount > account.balance:
    return (false, "Yetersiz hesap bakiyesi")
  return (true, "")

function checkATMCash(atm, amount):
  if amount > atm.totalCash(): return (false, "ATM'de yeterli nakit yok")
  // Ayrıca kullanılabilir banknot kombinasyonunu kontrol et
  combo = calculateDenominationCombination(atm.cashInventory, amount)
  if combo == null: return (false, "Uygun banknot kombinasyonu yok")
  return (true, combo)

function calculateDenominationCombination(inventory, amount):
  // Basit greedy yaklaşım (büyük banknottan başla). Eğer greedy başarısız olursa
  // daha gelişmiş kombinasyon araması (backtracking) uygulanabilir.
  denominations = sorted(keys(inventory), descending=True)
  remainder = amount
  use = {den:0 for den in denominations}
  for den in denominations:
    maxCount = min(inventory[den], floor(remainder / den))
    use[den] = maxCount
    remainder -= den * maxCount
  if remainder == 0: return use
  // Greedy başarısız ise backtracking/DP ile kombinasyon bul
  return findCombinationWithBacktracking(inventory, denominations, amount)

function dispenseCash(atm, combo):
  for den, count in combo.items():
    atm.cashInventory[den] -= count
  // Fiziksel donanım çağrısı: cashDispenser.dispense(combo)
  return true

function updateAccountAfterWithdrawal(account, amount):
  account.balance -= amount
  account.dailyWithdrawn += amount
  // veritabanına kaydet

function logTransaction(account, atm, amount, status, message):
  TransactionLog.append({time: now(), accountId: account.id, atmId: atm.id, amount, status, message})


// Ana işlem akışı
function main(card, enteredPIN):
  if ATM.status != "online": return error("ATM hizmet dışı")
  authResult = authenticate(card, enteredPIN)
  if not authResult[0]:
    logTransaction(null, ATM, 0, "fail", authResult[1])
    return error(authResult[1])
  account = authResult[1]

  // Kullanıcıdan işlem seçimi (burada sadece para çekme)
  selectedOperation = "withdraw"
  if selectedOperation == "withdraw":
    amount = getUserEnteredAmount()    // örn 100, 200, ... (sağlanan seçenekler)
    if amount <= 0: return error("Geçersiz miktar")

    ok, msg = checkDailyLimit(account, amount)
    if not ok:
      logTransaction(account, ATM, amount, "fail", msg)
      return error(msg)

    ok, msg = checkBalance(account, amount)
    if not ok:
      logTransaction(account, ATM, amount, "fail", msg)
      return error(msg)

    ok, comboOrMsg = checkATMCash(ATM, amount)
    if not ok:
      logTransaction(account, ATM, amount, "fail", comboOrMsg)
      return error(comboOrMsg)

    // Onay iste
    if not getUserConfirmation(amount): 
      logTransaction(account, ATM, amount, "cancel", "Kullanıcı iptal etti")
      return cancel("İşlem iptal edildi")

    // Nakit ver
    dispenseSuccess = dispenseCash(ATM, comboOrMsg)
    if not dispenseSuccess:
      logTransaction(account, ATM, amount, "fail", "Nakit dağıtım hatası")
      // gerektiğinde geri alma/rollback mekanizması
      return error("Nakit dağıtım hatası")

    // Hesabı güncelle
    updateAccountAfterWithdrawal(account, amount)
    logTransaction(account, ATM, amount, "success", "Çekim başarılı")

    // Makbuz/verme bildirimi
    if userWantsReceipt(): printReceipt(account, amount, comboOrMsg)

    return success("Para çekme işlemi başarılı")

// Backtracking kombinasyon arama (basit versiyon)
function findCombinationWithBacktracking(inventory, denominations, target):
  result = {}
  function backtrack(index, remainder, currentUse):
    if remainder == 0: return currentUse.copy()
    if index >= len(denominations): return null
    den = denominations[index]
    maxCount = min(inventory[den], floor(remainder / den))
    for c in range(maxCount, -1, -1):
      currentUse[den] = c
      res = backtrack(index+1, remainder - c*den, currentUse)
      if res != null: return res
    currentUse[den] = 0
    return null
  return backtrack(0, target, {den:0 for den in denominations})
