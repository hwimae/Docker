1. Start the container
docker compose up -d --build
docker ps

2. SSH into the container
ssh student@127.0.0.1 -p 2222
# password: student

3.Install XFCE4 + VNC inside the container
sudo apt update
sudo apt install -y xfce4 xfce4-goodies tigervnc-standalone-server dbus-x11 xauth x11-xserver-utils xfonts-base

4.Configure VNC
mkdir -p ~/.vnc
vncpasswd

5.Create ~/.vnc/xstartup
cat > ~/.vnc/xstartup << 'EOF'
#!/bin/sh
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS

export XDG_RUNTIME_DIR="/run/user/$(id -u)"
mkdir -p "$XDG_RUNTIME_DIR"
chmod 700 "$XDG_RUNTIME_DIR"

[ -f "$HOME/.Xresources" ] && xrdb "$HOME/.Xresources"

exec dbus-launch --exit-with-session startxfce4
EOF

chmod 700 ~/.vnc
chmod 600 ~/.vnc/passwd
chmod +x ~/.vnc/xstartup

6.Fix X11 socket permissions
sudo mkdir -p /tmp/.X11-unix
sudo chown root:root /tmp/.X11-unix
sudo chmod 1777 /tmp/.X11-unix

7. Start the VNC server
vncserver :1 -geometry 1366x768 -depth 24 -localhost yes

8. Create an SSH tunnel from Windows
ssh -N -L 5901:127.0.0.1:5901 -p 2222 student@127.0.0.1

9.Stop services
vncserver -kill :1
docker compose down
