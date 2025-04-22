#include <opencv2/opencv.hpp>
using namespace cv;

int main() {
    VideoCapture cap(0);
    if (!cap.isOpened()) return -1;

    int mode = 0;

    while (true) {
        Mat frame, output;
        cap >> frame;
        if (frame.empty()) break;
        switch (mode) {
        case 1: cvtColor(frame, output, COLOR_BGR2GRAY); break;
        case 2: GaussianBlur(frame, output, Size(9, 9), 0); break;
        case 3: bitwise_not(frame, output); break;
        default: output = frame.clone(); break;
        }

        imshow("Live Filter", output);

        char key = waitKey(30);
        if (key == 27) break;
        if (key >= '0' && key <= '3') mode = key - '0';
    }
    return 0;
}


