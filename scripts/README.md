

# SCRIPTS – INFRAESTRUCTURA 2 | ITLA

## 1. FORTIGATE – INTERFACES Y VLAN

```text
config system interface
    edit "port2"
        set alias "SW-USERS"
        set role lan
    next
    edit "port3"
        set alias "JUMP-SERVER"
        set ip 10.86.50.1 255.255.255.248
        set allowaccess ping https ssh
        set role lan
    next
    edit "port4"
        set alias "WEB-SERVER"
        set ip 10.86.60.1 255.255.255.248
        set allowaccess ping
        set role dmz
    next
    edit "VLAN10"
        set interface "port2"
        set vlanid 10
        set ip 10.86.10.1 255.255.255.128
        set allowaccess ping
        set role lan
    next
end
```

## 2. FORTIGATE – DHCP

```text
config system dhcp server
    edit 10
        set interface "VLAN10"
        set default-gateway 10.86.10.1
        set netmask 255.255.255.128
        set dns-service default
        config ip-range
            edit 1
                set start-ip 10.86.10.20
                set end-ip 10.86.10.100
            next
        end
    next
end
```

## 3. FORTIGATE – OBJETOS DE DIRECCIONES

```text
config firewall address
    edit "NET-VLAN10"
        set subnet 10.86.10.0 255.255.255.128
    next
    edit "NET-JUMP"
        set subnet 10.86.50.0 255.255.255.248
    next
    edit "NET-WEB"
        set subnet 10.86.60.0 255.255.255.248
    next
    edit "JUMP-SERVER"
        set subnet 10.86.50.2 255.255.255.255
    next
    edit "WEB-SERVER"
        set subnet 10.86.60.2 255.255.255.255
    next
end
```

## 4. FORTIGATE – POLÍTICAS DE FIREWALL

```text
config firewall policy
    edit 0
        set name "JUMP-TO-WEB"
        set srcintf "port3"
        set dstintf "port4"
        set srcaddr "JUMP-SERVER"
        set dstaddr "WEB-SERVER"
        set action accept
        set schedule "always"
        set service "HTTPS" "SSH" "RDP"
        set logtraffic all
    next
    edit 0
        set name "USERS-TO-JUMP-RDP"
        set srcintf "VLAN10"
        set dstintf "port3"
        set srcaddr "NET-VLAN10"
        set dstaddr "JUMP-SERVER"
        set action accept
        set schedule "always"
        set service "RDP"
        set logtraffic all
    next
    edit 0
        set name "DENY-USERS-SSH"
        set srcintf "VLAN10"
        set dstintf "port3"
        set srcaddr "NET-VLAN10"
        set dstaddr "JUMP-SERVER"
        set action deny
        set schedule "always"
        set service "SSH"
        set logtraffic all
    next
end
```

## 5. WINDOWS SERVER 2022 – JUMP SERVER

```powershell
ipconfig /all
route print
ping 10.86.50.1
ping 10.86.60.2

Test-NetConnection 10.86.60.2 -Port 443
Test-NetConnection 10.86.60.2 -Port 22
Test-NetConnection 10.86.60.2 -Port 3389

Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server" -Name "fDenyTSConnections" -Value 0
Enable-NetFirewallRule -DisplayGroup "Remote Desktop"

Get-ItemProperty "HKLM:\System\CurrentControlSet\Control\Terminal Server" -Name fDenyTSConnections

mstsc
```

## 6. UBUNTU – WEB SERVER

```bash
sudo apt update
sudo apt install -y nginx openssh-server xrdp xfce4 xfce4-goodies openssl

sudo systemctl enable --now nginx
sudo systemctl enable --now ssh
sudo systemctl enable --now xrdp

echo "startxfce4" > ~/.xsession

ip addr
ip route
ping -c 4 10.86.60.1

sudo systemctl status nginx --no-pager
sudo systemctl status ssh --no-pager
sudo systemctl status xrdp --no-pager

sudo ss -tulnp | grep -E ':(22|443|3389)\b'
```

## 7. UBUNTU – CERTIFICADO SSL Y HTTPS

```bash
sudo mkdir -p /etc/nginx/ssl

sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout /etc/nginx/ssl/caja.key \
  -out /etc/nginx/ssl/caja.crt \
  -subj "/CN=10.86.60.2"

sudo tee /etc/nginx/sites-available/caja > /dev/null <<'EOF'
server {
    listen 443 ssl;
    server_name 10.86.60.2;

    ssl_certificate /etc/nginx/ssl/caja.crt;
    ssl_certificate_key /etc/nginx/ssl/caja.key;

    root /var/www/html;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
EOF

sudo ln -sfn /etc/nginx/sites-available/caja /etc/nginx/sites-enabled/caja

sudo nginx -t
sudo systemctl restart nginx

curl -k -I https://127.0.0.1
curl -k -I https://10.86.60.2
```

## 8. FORTIGATE – COMANDOS DE VERIFICACIÓN

```text
get system status
get system interface
get router info routing-table all
show system dhcp server
show firewall address
show firewall policy
show vpn ssl settings
diagnose firewall iprope list
```

---

**Nota:** Configuraciones y comandos de referencia organizados a partir de la topología del laboratorio. No constituyen una exportación literal del running-config. La aplicación de comandos de configuración requiere verificar previamente el estado del equipo.
