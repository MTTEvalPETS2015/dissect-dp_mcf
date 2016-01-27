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
(2)

(n) show_bboxes_on_video.m
> - input_frames: in the format of image_path/%05d.jpg, which will be used with sprintf later.
> - there is statement of `dirlist = dir([input_frames '*.jpg']); `, but ***dirlist*** never get used inside function `show_bboxes_on_video`
