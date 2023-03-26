# Lab-5_202001263

202001263<br />
Pranav Thumar<br />



## IT314 - Software Engineering<br />
## Lab 5 - Static Analysis<br />


## Code:<br />
For this lab, the following code has been used for analysis:<br />
https://github.com/CT83/SmoothStream/blob/master/StreamViewer.py

```
import argparse

import cv2
import numpy as np
import zmq

from constants import PORT
from utils import string_to_image


class StreamViewer:
    def __init__(self, port=PORT):
        """
        Binds the computer to a ip address and starts listening for incoming streams.
        :param port: Port which is used for streaming
        """
        context = zmq.Context()
        self.footage_socket = context.socket(zmq.SUB)
        self.footage_socket.bind('tcp://*:' + port)
        self.footage_socket.setsockopt_string(zmq.SUBSCRIBE, np.unicode(''))
        self.current_frame = None
        self.keep_running = True

    def receive_stream(self, display=True):
        """
        Displays displayed stream in a window if no arguments are passed.
        Keeps updating the 'current_frame' attribute with the most recent frame, this can be accessed using 'self.current_frame'
        :param display: boolean, If False no stream output will be displayed.
        :return: None
        """
        self.keep_running = True
        while self.footage_socket and self.keep_running:
            try:
                frame = self.footage_socket.recv_string()
                self.current_frame = string_to_image(frame)

                if display:
                    cv2.imshow("Stream", self.current_frame)
                    cv2.waitKey(1)

            except KeyboardInterrupt:
                cv2.destroyAllWindows()
                break
        print("Streaming Stopped!")

    def stop(self):
        """
        Sets 'keep_running' to False to stop the running loop if running.
        :return: None
        """
        self.keep_running = False

def main():
    port = PORT

    parser = argparse.ArgumentParser()
    parser.add_argument('-p', '--port',
                        help='The port which you want the Streaming Viewer to use, default'
                             ' is ' + PORT, required=False)

    args = parser.parse_args()
    if args.port:
        port = args.port

    stream_viewer = StreamViewer(port)
    stream_viewer.receive_stream()


if __name__ == '__main__':
    main()
```

## Tool used:<br />
Pylint<br />


## Error Snippet:<br />
![image](https://user-images.githubusercontent.com/75677231/225576110-af95ce86-7007-43b5-9f4a-92ce24bd2748.png)

## Expalnation:<br />
1. Python shows the notification of 'Line too long' and has a limit of 80 characters. Hence, when the number of characters exceed, it shows this error.
2. In some of the old tools, it was a norm to keep a newline after the last line to prevent the compiler from reading random memory beyond the last line. Hence, pylint shows the error of 'Final newline missing' error.
3. Since the environment in which the libraries are stored and the code is stored are different, pylint is unable to import those libraries. Hence a few errors are shown because of that.
