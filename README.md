import http.server
import socketserver
import webbrowser
import threading
import sys
import time


sys.stdout.reconfigure(encoding='utf-8') if hasattr(sys.stdout, 'reconfigure') else None

PORT = 8080
DIRECTORY = "."

class Handler(http.server.SimpleHTTPRequestHandler):
    def __init__(self, *args, **kwargs):
        super().__init__(*args, directory=DIRECTORY, **kwargs)

    def log_message(self, format, *args):
   
        pass

def start_server():
    socketserver.TCPServer.allow_reuse_address = True
    try:
        with socketserver.TCPServer(("", PORT), Handler) as httpd:
            print(f"[Server] Serving birthday greeting at http://localhost:{PORT}")
            httpd.serve_forever()
    except Exception as e:
        print(f"[Error] Could not start server: {e}")

if __name__ == "__main__":
    # Start server in background thread so the app can continue
    server_thread = threading.Thread(target=start_server, daemon=True)
    server_thread.start()

    # Wait a fraction of a second for server to initialize
    time.sleep(0.5)

    # Open local greeting page in default browser
    url = f"http://localhost:{PORT}/index.html"
    print(f"[Launcher] Opening {url} in your web browser...")
    webbrowser.open(url)

    # Keep script running to maintain the server
    print("[Launcher] Close the browser or press Ctrl+C in this terminal to stop.")
    try:
        while True:
            time.sleep(1)
    except KeyboardInterrupt:
        print("\n[Launcher] Server stopped. Have a wonderful birthday celebration!")
        sys.exit(0)
