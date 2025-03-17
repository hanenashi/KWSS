<h1>KWSS: Kiwi Web Server Scripts for Termux</h1>
<p>KWSS is a pair of Bash scripts for Termux on Android that use Python’s <code>http.server</code> to serve files from <code>/storage/emulated/0</code> over HTTP. <code>kwss</code> starts the server, and <code>kwsq</code> stops it. Built for simplicity and network access, it’s a lightweight way to share your Android storage.</p>

<h2>Prerequisites</h2>
<ul>
    <li><strong>Android Device</strong>: Running Termux (tested on modern Android, e.g., Pixel 8 with Android 15).</li>
    <li><strong>Termux</strong>: Install from <a href="https://f-droid.org/packages/com.termux/">F-Droid</a>.</li>
    <li><strong>Python</strong>: Install via <code>pkg install python</code> in Termux.</li>
    <li><strong>Storage Access</strong>: Run <code>termux-setup-storage</code> to grant access to <code>/storage/emulated/0</code>.</li>
</ul>

<h2>Installation</h2>
<ol>
    <li><strong>Update Termux</strong>:
        <pre><code>pkg update && pkg upgrade</code></pre>
    </li>
    <li><strong>Install Python</strong>:
        <pre><code>pkg install python</code></pre>
    </li>
    <li><strong>Add Scripts</strong>:
        <p>Copy the scripts below into Termux’s <code>/usr/bin/</code> directory.</p>
        <p>Make them executable with <code>chmod</code>.</p>
    </li>
</ol>

<h3>Scripts</h3>
<p>Here’s the full code for easy copying:</p>

<h4>kwss (Start Server)</h4>
<pre><code>#!/data/data/com.termux/files/usr/bin/sh

# Check if storage is accessible
cd /storage/emulated/0 || {
    echo "Error: Cannot access /storage/emulated/0. Run 'termux-setup-storage' first."
    exit 1
}

# Check if Python is installed
if ! command -v python3 >/dev/null 2>&1; then
    echo "Error: Python3 not found. Install it with 'pkg install python'."
    exit 1
fi

# Start Python HTTP server on port 8080
python3 -m http.server 8080 --bind 0.0.0.0 &>/dev/null &
PID=$!
echo "Server started on http://$(ip -4 addr show wlan0 | grep -oP '(?<=inet\s)\d+(\.\d+){3}'):8080 (serving /storage/emulated/0)"
echo "Process ID: $PID"
</code></pre>

<h4>kwsq (Stop Server)</h4>
<pre><code>#!/data/data/com.termux/files/usr/bin/sh

# Stop Python HTTP server
if pkill -f "python3 -m http.server" >/dev/null 2>&1; then
    echo "Server stopped."
else
    echo "No running server found."
fi
</code></pre>

<h4>Install Commands</h4>
<ul>
    <li><strong>kwss</strong>:
        <pre><code>echo -e '#!/data/data/com.termux/files/usr/bin/sh\n\n# Check if storage is accessible\ncd /storage/emulated/0 || {\n    echo "Error: Cannot access /storage/emulated/0. Run '\''termux-setup-storage'\'' first."\n    exit 1\n}\n\n# Check if Python is installed\nif ! command -v python3 >/dev/null 2>&1; then\n    echo "Error: Python3 not found. Install it with '\''pkg install python'\''."\n    exit 1\nfi\n\n# Start Python HTTP server on port 8080\npython3 -m http.server 8080 --bind 0.0.0.0 &>/dev/null &\nPID=$!\necho "Server started on http://$(ip -4 addr show wlan0 | grep -oP '\''(?<=inet\\s)\\d+(\\.\d+){3}'\''):8080 (serving /storage/emulated/0)"\necho "Process ID: $PID"' > /data/data/com.termux/files/usr/bin/kwss
chmod +x /data/data/com.termux/files/usr/bin/kwss</code></pre>
    </li>
    <li><strong>kwsq</strong>:
        <pre><code>echo -e '#!/data/data/com.termux/files/usr/bin/sh\n\n# Stop Python HTTP server\nif pkill -f "python3 -m http.server" >/dev/null 2>&1; then\n    echo "Server stopped."\nelse\n    echo "No running server found."\nfi' > /data/data/com.termux/files/usr/bin/kwsq
chmod +x /data/data/com.termux/files/usr/bin/kwsq</code></pre>
    </li>
</ul>

<h2>Usage</h2>
<ul>
    <li><strong>Start</strong>: Run <code>kwss</code> in Termux.
        <ul>
            <li>Access the server from any device on your network (e.g., <code>http://192.168.1.x:8080</code>).</li>
        </ul>
    </li>
    <li><strong>Stop</strong>: Run <code>kwsq</code> to kill the server.</li>
</ul>

<h2>Troubleshooting</h2>
<ul>
    <li><strong>No IP Shown</strong>: Ensure Wi-Fi is on (<code>wlan0</code> interface required).</li>
    <li><strong>Permission Denied</strong>: Rerun <code>termux-setup-storage</code> or check script permissions (<code>ls -l /data/data/com.termux/files/usr/bin/kwss</code> should show <code>-rwxr-xr-x</code>).</li>
    <li><strong>Python Missing</strong>: Install with <code>pkg install python</code>.</li>
</ul>

<h2>Notes</h2>
<ul>
    <li>Runs on port 8080 by default—edit <code>kwss</code> to change it.</li>
    <li>Serves all of <code>/storage/emulated/0</code>—careful what you share!</li>
    <li>Tested on Termux with Python 3.11 as of March 2025.</li>
</ul>

<p>Enjoy serving your files with KWSS!</p>
