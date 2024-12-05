# Image_Mosaicing
This project implements image mosaicing using computer vision techniques. The workflow includes detecting feature points using SIFT , finding correspondences between images, estimating the homography matrix, and blending images to create seamless mosaics. The project supports mosaicing of two or three images, producing high-quality panoramic views.

# 1.1 Getting Correspondences

To establish correspondences between the first image and the second frame of the video, the SIFT descriptor from OpenCV was used to detect keypoints and descriptors in both images. A brute-force matcher was applied with the KNN matching method (size=2) to find the two best matches for each descriptor. To filter out good matches, a ratio test was applied, retaining only matches where the distance of the closest match is significantly less than the distance of the second-closest match.
Implementation Steps:

1.Extract keypoints and descriptors using the SIFT algorithm.
2.Match descriptors using the KNN method.
3.Apply a ratio test to filter good correspondences.

# 1.2 Compute the Homography Parameters
To align points between the First image and the Second image, a homography matrix H was computed. This 3×3 matrix transforms points from one view into their corresponding points in another view.
Implementation Steps: (Using the DLT Method)

1.Select a minimum of 4 pairs of corresponding points between the two views.
2.Construct a system of linear equations Ax=b, where A is derived from the correspondences, b represents the point coordinates, and x contains the homography parameters.
3.Solve for x using least squares, setting H3,3=1 to normalize the matrix.
4.Verify the homography matrix by mapping points from the book cover image to the video frame.

# 1.3 Warping Between Image Planes
Warping an image involves transforming it using the recovered homography matrix H. This transformation maps points from the source image to a new perspective in the destination image plane.
Implementation Details:
1.Forward Warping:

The homography matrix H transforms each pixel p in the source image to a new pixel p′ in the destination image such that p′=H⋅p , where p and p′ are represented in homogeneous coordinates.
2.Sub-pixel Interpolation:

Transformed coordinates are often sub-pixel values. To address this, bilinear interpolation was used to distribute pixel values to nearby destination pixels. This ensures smooth warping without gaps or aliasing artifacts.

3-Inverse Warping:

To prevent holes in the output image, an inverse warping approach was employed:
▪ Points from the destination image were warped back into the source image to determine their corresponding source coordinates.
▪ The bounding box of the transformed region in the source image was identified, and all pixels within this bounding box were sampled.
▪ BiLinear interpolation was applied to compute the pixel values at non-integer coordinates.

# 1.4 Creating the Output Mosaic
Using the warped source image, a mosaic was created by merging the registered views of the two images.
Implementation Details:

1.Create an Output Canvas:

A new image canvas was initialized, large enough to accommodate both the warped source image and the destination image.
2.Overlay Images:

The warped source image was overlaid onto the destination image.
Areas without data (holes) in the warped image were left black.

3.Handle Overlapping Regions:
In regions where the warped image overlapped with the destination image:

A simple pixel averaging was performed to merge the two images smoothly.

Alternatively, for non-overlapping regions, pixels from the respective images were retained without modification.

