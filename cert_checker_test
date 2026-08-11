Clear-Host

Write-Host "============================================" -ForegroundColor Cyan
Write-Host "       SMART CARD CERTIFICATE CHECK" -ForegroundColor Cyan
Write-Host "============================================" -ForegroundColor Cyan
Write-Host ""

Write-Host "Checking smart card..." -ForegroundColor Yellow
Write-Host ""

# Get smart card information
$output = certutil.exe -scinfo 2>&1 | Out-String

# --------------------------------------------
# Check if a card is actually inserted
# --------------------------------------------

if ($output -match "SCARD_STATE_PRESENT" -or
    $output -match "Card:" -and $output -notmatch "No card") {

    Write-Host "STATUS : CARD DETECTED" -ForegroundColor Green
}
else {
    Write-Host "STATUS : NO CARD DETECTED" -ForegroundColor Red
    Write-Host ""
    Write-Host "Please insert your USB smart card/token." -ForegroundColor Yellow
    Write-Host ""

    Read-Host "Press Enter to exit"
    exit
}

Write-Host ""

# --------------------------------------------
# Find reader
# --------------------------------------------

$reader = $null

if ($output -match "--- Reader:\s*(.+)") {
    $reader = $matches[1].Trim()
}

Write-Host "--------------------------------------------" -ForegroundColor DarkGray
Write-Host "SMART CARD" -ForegroundColor Cyan
Write-Host "--------------------------------------------"

if ($reader) {
    Write-Host "Reader        : $reader"
}

# --------------------------------------------
# Extract certificate information
# --------------------------------------------

$certInfo = certutil.exe -scinfo 2>&1 | Out-String

# Subject
$subject = $null
if ($certInfo -match "Subject\s*=\s*(.+)") {
    $subject = $matches[1].Trim()
}

# Issuer
$issuer = $null
if ($certInfo -match "Issuer\s*=\s*(.+)") {
    $issuer = $matches[1].Trim()
}

# Serial number
$serial = $null
if ($certInfo -match "Serial Number:\s*([0-9A-Fa-f\s]+)") {
    $serial = $matches[1].Trim() -replace "\s+", ""
}

# Thumbprint
$thumbprint = $null
if ($certInfo -match "Cert Hash\(sha1\):\s*([0-9A-Fa-f\s]+)") {
    $thumbprint = $matches[1].Trim() -replace "\s+", ""
}

# Valid from
$notBefore = $null
if ($certInfo -match "NotBefore:\s*(.+)") {
    $notBefore = $matches[1].Trim()
}

# Valid until
$notAfter = $null
if ($certInfo -match "NotAfter:\s*(.+)") {
    $notAfter = $matches[1].Trim()
}

# --------------------------------------------
# Display certificate information
# --------------------------------------------

Write-Host ""
Write-Host "--------------------------------------------" -ForegroundColor DarkGray
Write-Host "CERTIFICATE INFORMATION" -ForegroundColor Cyan
Write-Host "--------------------------------------------"

if ($subject) {
    Write-Host "Subject       : $subject"
}

if ($issuer) {
    Write-Host "Issuer        : $issuer"
}

if ($serial) {
    Write-Host "Serial Number : $serial"
}

if ($thumbprint) {
    Write-Host "Thumbprint    : $thumbprint"
}

if ($notBefore) {
    Write-Host "Valid From    : $notBefore"
}

if ($notAfter) {
    Write-Host "Valid Until   : $notAfter"
}

# --------------------------------------------
# Certificate expiration
# --------------------------------------------

if ($notAfter) {

    try {
        $expiryDate = [DateTime]::Parse($notAfter)
        $today = Get-Date

        $daysRemaining = ($expiryDate - $today).Days

        Write-Host ""

        Write-Host "Days Remaining: $daysRemaining"

        if ($daysRemaining -lt 0) {

            Write-Host "STATUS        : EXPIRED" -ForegroundColor Red

        }
        elseif ($daysRemaining -le 60) {

            Write-Host "STATUS        : EXPIRING WITHIN 60 DAYS" -ForegroundColor Yellow

        }
        else {

            Write-Host "STATUS        : VALID" -ForegroundColor Green

        }
    }
    catch {
        Write-Host "STATUS        : Unable to calculate expiry" -ForegroundColor Yellow
    }
}

Write-Host ""
Write-Host "============================================" -ForegroundColor Cyan
Write-Host "                 COMPLETE" -ForegroundColor Green
Write-Host "============================================" -ForegroundColor Cyan
Write-Host ""

Read-Host "Press Enter to exit"
