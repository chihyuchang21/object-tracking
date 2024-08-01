# Object Tracking using OpenCV

This project demonstrates a simple object tracking application using OpenCV in Python. The script detects and tracks moving objects in a video feed from a stable camera. The project is based on the tutorial from the YouTube video ["Object Tracking with OpenCV and Python"](https://www.youtube.com/watch?v=O3b8lVF93jU&list=LL&index=11&t=417s).

## Requirements

- Python 3.x
- OpenCV (`cv2`)
- `tracker` module (Ensure you have the `EuclideanDistTracker` class available)

## Installation

1. Install Python 3.x from the official [Python website](https://www.python.org/).
2. Install OpenCV library using pip:

    ```bash
    pip install opencv-python
    ```

3. Ensure you have the `tracker` module with `EuclideanDistTracker` class. If you do not have it, you can implement a simple Euclidean distance tracker.

## Usage

1. Clone this repository or download the script.
2. Place your video file (`highway.mp4`) in the `object_tracking` directory.
3. Run the script:

    ```bash
    python object_tracking.py
    ```

## Script Explanation

The script performs the following steps:

1. **Initialize Tracker and Video Capture:**

    ```python
    tracker = EuclideanDistTracker()
    cap = cv2.VideoCapture("object_tracking/highway.mp4")
    ```

2. **Set Up Object Detection:**

    ```python
    object_detector = cv2.createBackgroundSubtractorMOG2(history=100, varThreshold=40)
    ```

3. **Read Frames from Video and Detect Objects:**

    ```python
    while True:
        ret, frame = cap.read()
        if not ret:
            break

        # Extract Region of Interest (ROI)
        roi = frame[340:720, 500:800]

        # Object Detection
        mask = object_detector.apply(roi)
        _, mask = cv2.threshold(mask, 254, 255, cv2.THRESH_BINARY)
        contours, _ = cv2.findContours(mask, cv2.RETR_TREE, cv2.CHAIN_APPROX_SIMPLE)
        detections = []
        for cnt in contours:
            area = cv2.contourArea(cnt)
            if area > 100:
                x, y, w, h = cv2.boundingRect(cnt)
                detections.append([x, y, w, h])

        # Object Tracking
        boxes_ids = tracker.update(detections)
        for box_id in boxes_ids:
            x, y, w, h, id = box_id
            cv2.putText(roi, str(id), (x, y - 15), cv2.FONT_HERSHEY_PLAIN, 2, (255, 0, 0), 2)
            cv2.rectangle(roi, (x, y), (x + w, y + h), (0, 255, 0), 3)

        # Display the frames
        cv2.imshow("ROI", roi)
        cv2.imshow("Frame", frame)
        cv2.imshow("Mask", mask)

        key = cv2.waitKey(30)
        if key == 27:   # Press ESC to break the loop
            break

    cap.release()
    cv2.destroyAllWindows()
    ```

## Notes

- Ensure the video path is correct.
- Adjust the Region of Interest (ROI) coordinates as per your video.
- Fine-tune the object detector parameters (`history` and `varThreshold`) based on your use case.

## Areas for Improvement

- **Counting Error:** The current implementation has counting errors that need to be improved.

## License

This project is open-source and available under the MIT License.
