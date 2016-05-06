# dissect-dp_mcf

### Breakdown steps
(1) compile 3rd-party libraries
> - cs2
> - voc-release3.1

```matlab
mex -O resize.cc
mex -O dt.cc
mex -O features.cc

% use one of the following depending on your setup
% 1 is fastest, 3 is slowest 

% 1) multithreaded convolution using blas
% mex -O fconvblas.cc -lmwblas -o fconv
% 2) mulththreaded convolution without blas
% mex -O fconvMT.cc
% 3) basic convolution, very compatible
mex -O fconv.cc
```
(2) show_bboxes_on_video.m
> - input_frames: in the format of image_path/%05d.jpg, which will be used with sprintf later.
> - there is statement of `dirlist = dir([input_frames '*.jpg']); `, but ***dirlist*** never get used inside function `show_bboxes_on_video`

(3) about DPM data format
>- main function used from DPM is `function [boxes] = detect(input, model, thresh, bbox, overlap, label, fid, id, maxsize)`
>- The function returns a matrix with one row per detected object.  The last column of each row gives the score of the detection. The column before last specifies the component used for the detection. The first 4 columns specify the bounding box for the root filter and subsequent columns specify the bounding boxes of each part. 
> - So each **_boxes_** has 30 columns, with 6 parts each has 4 value for box, plus last two columns explained in the above point. 4*6+2 = 30
> - secondary function used from DPM is `function bbox = getboxes(model, boxes)`
> - Predict bounding boxes from root and part locations. So each **_bbox_** has 5 columns, [x1,y1,x2,y2,score]

(4) about dp_mcf data format
> - For each image, run 
```
boxes = detect(im, model, thresh);  %% running the detector
bbox =  getboxes(model, boxes);
bboxes(i).bbox = nms(bbox, 0.5); 
```
> - So the chain of data flow is: boxes-->bbox-->bboxes. **NOTE** that bboxes is not used at all in DP_MCF, because the bounding box values are saved in different ways.

(5) about `bboxes2dres` and `dres2bboxes`
> - For `bboxes2dres`, DPM-->DP_MCF, including [x1,y1,x2,y2,score]-->[x,y,w,h,r,fr] (note: r==score) and 1xN struct-->1xM struct, where M = N x D
> - For `dres2bboxes`, the reverse process of `bboxes2dres`. However now dres should be processed by tracker hence has new filed 'id'.
