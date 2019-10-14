import re
import threading
from urllib.request import urlopen

lock = threading.Lock()
total_count = 0

def read_url(url):
    print(url)
    global total_count
    html_file = urlopen(url)
    html_string = html_file.decode().read()
    p = re.compile(r'Django 2[.]0')
    res = p.findall(html_string)
    print(res)
    local_count = len(res)
    lock.acquire()
    total_count += local_count
    lock.release()

def main():
    web_string = input().strip()
    print(web_string)
    p = re.compile(r'^(.+)\s+(\d+)$')
    res = p.findall(web_string)
    print(res)
    web_url = res[0][0]
    print('web_url = {}'.format(web_url))
    thread_count = int(res[0][1])
    print('thread_count = {}'.format(thread_count))
    for t in range(thread_count):
        my_thread = threading.Thread(target=read_url , args=(web_url+ '?page={}'.format(t+1),))
        my_thread.start()
        my_thread.join()

if __name__ == '__main__':
    main()
