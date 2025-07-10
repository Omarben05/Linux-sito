# Linux-sito

#!/bin/bash

# 1. Creazione utenti
sudo adduser webadmin
sudo adduser editor

# 2. Creazione gruppo sito e aggiunta utenti
sudo groupadd sito
sudo usermod -aG sito webadmin
sudo usermod -aG sito editor

# 3. Creazione directory del sito
sudo mkdir -p /var/www/omarsito

# 4. Cambia gruppo della directory
sudo chown -R root:sito /var/www/omarsito

# 5. Permessi: solo root + gruppo possono scrivere, altri solo leggere
sudo chmod -R 2755 /var/www/omarsito

# 6. Installazione Apache2
sudo apt update
sudo apt install apache2 -y

# 7. Creazione pagina index.html
sudo bash -c 'cat > /var/www/omarsito/index.html' <<EOF
<!DOCTYPE html>
<html lang="it">
<head>
  <meta charset="UTF-8">
  <title>Home</title>
</head>
<body>
  <h1>Benvenuto nel mio sito</h1>
</body>
</html>
EOF

# 8. Riavvio Apache2
sudo systemctl restart apache2

# 9. Creazione cartella backup
sudo mkdir -p /home/webadmin/backup
sudo chown webadmin:sito /home/webadmin/backup

# 10. Script di backup (backup_web.sh)
cat > /home/webadmin/backup_web.sh <<'EOF'
#!/bin/bash

# Percorso della cartella del sito
SOURCE_DIR="/var/www/omarsito"

# Percorso dove salvare il backup
DEST_DIR="/home/webadmin/backup"

# Crea la cartella di backup se non esiste
mkdir -p "$DEST_DIR"

# Timestamp con data e ora correnti
TIMESTAMP=$(date +"%Y-%m-%d_%H-%M")

# Nome del file di backup
BACKUP_NAME="backup_omarsito_${TIMESTAMP}.tar.gz"

# Crea il file compresso .tar.gz
tar -czf "$DEST_DIR/$BACKUP_NAME" "$SOURCE_DIR"

# Messaggio finale
echo "Backup completato: $DEST_DIR/$BACKUP_NAME"
EOF

# 11. Rendi eseguibile lo script
chmod +x /home/webadmin/backup_web.sh
chown webadmin:sito /home/webadmin/backup_web.sh

