# StreamlitとYOLOでアプリ作成

Streamlit と YOLOを掛け合わせてアプリを作成しましよう。

## 画像から物体を検出する

```python
import cv2
import streamlit as st
from PIL import Image
from ultralytics import YOLO


def app():
    st.title("物体検出アプリ")
    model = YOLO('yolov8n.pt')
    class_names_map = model.names

    with st.form("my_form"):
        selected_classes = st.multiselect('検出したいクラスを選択してください', class_names_map.values(), default=['apple', 'person']) 
        uploaded_image = st.file_uploader("画像を選択してください", type=["jpg", "jpeg", "png"])
        st.form_submit_button(label='Submit')

    if uploaded_image:
        image = Image.open(uploaded_image)
        st.image(image, caption="Uploaded Image.", use_column_width=True)  # Display the uploaded image
        keys = [key for key, value in class_names_map.items() if value in selected_classes]
        results = model(image, classes=keys)

        annotated_image = results[0].plot()  # OpenCV形式（BGR）で返される
        # StreamlitはPIL形式（RGB）を期待しているため BGRからRGBに変換
        annotated_image_rgb = cv2.cvtColor(annotated_image, cv2.COLOR_BGR2RGB)
        annotated_image_pil = Image.fromarray(annotated_image_rgb)
        st.image(annotated_image_pil, caption="Detected Objects.", use_column_width=True)


if __name__ == "__main__":
    app()
```

## 動画から物体を検出する


```python
import cv2
import streamlit as st
import tempfile
from ultralytics import YOLO


def app():
    st.title("物体検出アプリ")
    model = YOLO('yolov8n.pt')
    class_names_map = model.names

    with st.form("my_form"):
        selected_classes = st.multiselect('検出したいクラスを選択してください', class_names_map.values(), default=['person'])
        uploaded_video = st.file_uploader("動画を選択してください", type=["mp4", "mov"])
        st.form_submit_button(label='Submit')

    if uploaded_video:
        temp_file = tempfile.NamedTemporaryFile(delete=False)
        temp_file.write(uploaded_video.read())

        # Open the video file
        video_cap = cv2.VideoCapture(temp_file.name)
        stframe = st.empty()
        keys = [key for key, value in class_names_map.items() if value in selected_classes]

        while video_cap.isOpened():
            ret, frame = video_cap.read()
            if not ret:
                break

            results = model(frame, classes=keys)
            annotated_frame = results[0].plot()

            # StreamlitはPIL形式（RGB）を期待しているため BGRからRGBに変換
            stframe.image(cv2.cvtColor(annotated_frame, cv2.COLOR_BGR2RGB), use_column_width=True)

        video_cap.release()  # Release the video capture object

if __name__ == "__main__":
    app()
```

- 参考：[https://docs.opencv.org/4.x/dd/d43/tutorial_py_video_display.html](https://docs.opencv.org/4.x/dd/d43/tutorial_py_video_display.html)

:::{card} ヒント
- RGB ＝ Pillowでの色の順はRGB（赤、緑、青）を前提としている。
- BGR ＝ OpenCVの関数imread()で画像ファイルを読み込むとBGR（青、緑、赤）になる。
:::


:::{card} topic
当初、最終的に物体検出後の動画を作成してStreamlitで表示しようと思いましが、以下の点であきらめました。
- Web上で再生するにはH.264形式でコーデックする必要がある
- 通常のpipでinstallする opencv-python では、H.264形式での動画出力に対応していないため
  - pip install --no-binary opencv-python でソースからビルドする必要がある
    - またcloudでは `opencv-python-headless` しか対応していない

Downloadボタンなどを配置して、作成したファイルをローカルで表示する分には問題なく再生されるので、お時間ある方は試してみてください。
:::



## 2つの機能をもったアプリを公開

- [demoURL](https://yolo-object-detection-abavrjes8taktln9dpuejj.streamlit.app/)
- [github](https://github.com/masakos/yolo-object-detection-use-streamlit)
  - [Create a multipage app](https://docs.streamlit.io/get-started/tutorials/create-a-multipage-app) を参考に画像と動画のpagesを作成


#### Let's Try🤗!!
お時間ある方は　カスタマイズしたり、写真やビデオをとっていろいろな物体が検出できか試してみてください!!
