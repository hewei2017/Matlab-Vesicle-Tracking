# Matlab-Vesicle-Tracking
Matlab single vesicle tracking with Matlab

The single vesciel tracking algorithm is based on the single-particle tracking one that was used to obtain the position r(t) of an individual vesicle in a single frame t, and the trajectory of the vesicle constructed by linking the positions identified as belonging to the same vesicle over time [1]. Several adaptions increased tracking accuracy. We revised the previous protocol and carried out the single vesicle tracking according to the following procedures: 

1)	Reducing noise of the raw image. The background of the fluorescent protein- labeled cell is quite noisy because of autofluorescence of the live cell and unbound fluorescent proteins. The gain of the camera/photomultiplier tubes (PMTs) also produces the entire image background. However, the size of the whole cell autofluorescence is much larger than the size of the vesicles, and the size of the dark current by the high camera/PMT gain is much smaller than the vesicle. We thus applied a band-pass filter to the raw image and subtracted a constant intensity threshold from each image. We noticed that the single vesicle photobleached very quickly. As a result, we calculated the average intensity bleach curve for each video and fitted the curve with an exponential function. We then adjusted the intensity threshold according to the average intensity bleach function. 

[thd1all, thd2all] = thdFP(fname, init,final) 

2)	Setting the segmentation mask for the region of interest, excluding the cilia. After the de-noise in step 1), we found there are still irregular shapes labeled by the fluorescent protein markers, especially at the cell boundary. We next define the segmentation mask for the region of interest for tracking, which is an area that covers the nuclear region of the cell and around ~ 15 um in diameter in the center of the ciliary base. We also exclude the area covered by cilia from the mask because the linear-shaped bright cilia will introduce errors in vesicle recognition. The mask is generated in MATLAB using the Image Segmenter app. 

[BW,MASKEDIMAGE] = segmentImage(X)

3)	Determining the location of the vesicle. The precise coordinates of the vesicle are determined from fitting the center of the cross-correlation matrix between the processed image and an isotropic 11 x 11 standard Gaussian kernel matrix. 

[xys,PreProcessSequence] = vesiclepreconf(fname,init,final,testframeno)

4)	Preforming the single-vesicle tracking with the nearest neighbor algorithm [2]. The algorithm requires the empirically selected threshold for the searching radius r. The value of r is determined by an average of half the distance between vesicles. The position of the vesicle in the next frame is determined by the nearest location within the distance r. This search of next location of the vesicle applied to the frame range of the next 1-5 frames. If no vesicles appear in the following five frames, the vesicle is considered as lost.

tracks = track_addbri(xys,maxdisp,iz)

5)	Manually adjust the missed-linked trajectories. The cargo vesicles exhibit tremendous heterogeneity in their luminosities, moving speeds and density in distribution. These characteristics produced a lot of tracking errors in the nearest neighbor algorithm. To correct the missed linked trajectories, we supplement the trajectories from step 5 with further manual correction. A set of MATLAB code is developed to break the trajectories, add new positions to the trajectories, exchange trajectories segments between two trajectories, or delete positions of the trajectories. A final collection of the trajectories is evaluated by the overlay of the trajectories with the vesicles in the raw image.

posp1 = vesicle_display(xys,imagef, frameno)
res1=vesicle_add(id, res,xnew, ynew, tnew)
res1=vesicle_break(id, res, frameno)
res2=vesicle_fragex(id1, id2, res, frameno1, frameno2, exchange)
trackshow_frame(res,fname,imageno)
function mov = trackshow_mov(res,fname,istart, ifinal)


[1]He, W. et al. Dynamic heterogeneity and non-Gaussian statistics for acetylcholine receptors on live cell membrane. Nature Communications 7, 11701, doi:10.1038/ncomms11701 https://www.nature.com/articles/ncomms11701#supplementary-information (2016).
[2]Crocker, J. C. & Grier, D. G. Methods of Digital Video Microscopy for Colloidal Studies. Journal of Colloid and Interface Science 179, 298-310, doi:https://doi.org/10.1006/jcis.1996.0217 (1996).
