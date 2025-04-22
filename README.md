#include <opencv2/opencv.hpp>
using namespace cv;

int r = 0, g = 0, b = 0;

void on_trackbar(int, void*) 
{
    Mat color(300, 300, CV_8UC3, Scalar(b, g, r)); // 注意順序為 BGR
    imshow("Color Mixer", color);
}



Mat canvas(500, 500, CV_8UC3, Scalar(255, 255, 255));
Point prev(-1, -1);
bool drawing = false;

void draw(int event, int x, int y, int flags, void*) {
    if (event == EVENT_LBUTTONDOWN) {
        drawing = true;
        prev = Point(x, y);
    }
    else if (event == EVENT_MOUSEMOVE && drawing) {
        line(canvas, prev, Point(x, y), Scalar(0, 0, 0), 2);
        prev = Point(x, y);
        imshow("Painter", canvas);
    }
    else if (event == EVENT_LBUTTONUP) {
        drawing = false;
    }
}



int main() {


    //畫布
    namedWindow("Color Mixer", WINDOW_AUTOSIZE);

    createTrackbar("Red", "Color Mixer", &r, 255, on_trackbar);
    createTrackbar("Green", "Color Mixer", &g, 255, on_trackbar);
    createTrackbar("Blue", "Color Mixer", &b, 255, on_trackbar);

    on_trackbar(0, 0); // 初始顯示

    while (true) {
        if (waitKey(20) == 27) break; // 按下 ESC 離開
    }

    //繪圖

    namedWindow("Painter");
    setMouseCallback("Painter", draw);

    imshow("Painter", canvas);
    while (true) {
        if (waitKey(20) == 27) break;
    }


    //鏡頭

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


