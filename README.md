# vehicle-detection

Many of these concepts are explained with figures in the corresponding Jupyter
notebook.

## Histogram of Oriented Gradients (Hog)

1. The function get_hog_features is used to compute HOG. In the cell below you
can find an example image of a car and what the corresponding HOG image looks
like.

2. After exploring different HOG combinations I settled on using a YCrCb color
space with orient = 9, pix_per_cell = 8, and cell_per_block = 2.

## Classifier

3. I found that I got great results when using all three color channels of my
HOG features along with a color histogram with 32 bins for each color channel.
This was trained using the scikit LinearSVC model with default parameters on
all the images found in the training set after randomizing and splitting into
test and training sets.

## Sliding Window Search

1. The sliding window search is implemented in the slide_window function. After
playing around with some parameters I found using an overlap of 0.5 in each
direction and using a window sizes 64, 74, 84, and 94 gave good results when
scanning the frame for cars.

2. After that I clustered all the windows which detected cars based on which
windows overlapped, this gave a distinct car. From here I took the x/y min/max
of all the windows to generate a single window which represented a single
bounding box for the car. Images for the individual frames can be found in
Combining Sliding Windows and Classifier. For individual frames I did not do
anything to detect false positives as it is a much easier task with context
(aka the video).

## Video

1. The final video can be found in with_cars.mp4 (or at the end of the
notebook).

2. To prevent false positives I kept track of all found cars in the previous
images. If a car was seen in the relative same position (meaning their bounding boxes overlapped) for the previous 8 frames then we considered it a true
positive. In addition to this I kept track of the previous bounding box the
car was found in and averaged the previous 8 frames. This allowed the bounding
box appear smoother in the video. In some cases the car would not be seen in
the frame, in this case we would still remember the position it was last seen
and if the car has not been seen in the last 10 frames then we forget it.

## Limitations

On my laptop this whole process took around 20mins to compute. This is far too
slow as these detections need to happen in realtime on an actual car.

Depending on the framerate extremely fast objects may not be detected as the
distance between the car in one frame vs the previous may be too fast.

This did not handle the case where two cars overlap. To do this we need to do
a better job of distinguising one car from another and keep track if one car
is behind another.
