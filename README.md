
# INTERPRET Sinlge Agent Prediction in the ICAPS21/ICCV21 Stage

In this part, the input is M agents' motion information including coordinates, velocities, yaw, vehicle length and width in the observed 1 second (10 frames) as well as the cooresponding HD map. The target is to predict one target agents' coordinates and yaw in the future 3 seconds (30 frames).

Note that this instruction is for the **ICAPS21/ICCV21** Stage of the INTERPRET challenge with the latest version of INTERACTION Dataset (**v1.2**). As for the guidance for the NeurIPS20 stage with the deprecated version of INTERACTION Dataset (v1.1), please check https://github.com/interaction-dataset/INTERPRET_challenge_regular_generalizability_track. However, since the v1.1 version is deprecated, we suggest participants to use the latest version of data and submit to the latest stage of competition.

## Submission for Single-Agent Prediction in the ICAPS21/ICCV21 Stage

Please first read [the old guideline](http://challenge.interaction-dataset.com/leader-board-introduction) for the basic information about the input data and submission. Most of them still applies.

The following are the changed parts:

For each scenario X like (DR_CHN_Merging_ZS0), there should be a single file 'X_sub.csv'. The following columns would be used during the evaluation: case_id, track_id, frame_id, timestamp_ms, x1, y1, x2, y2, x3, y3, x4, y4, x5, y5, x6, y6. The order of rows and columns could be arbitrary. 'xi, yi' represents the predicted coordinate for the vehicle 'track_id' at 'timestamp_ms' in the modality i. Up to 6 modalities would be taken into consideration. Participants could submit less than 6 modalities (like only x1, y1).

Only the one vehicle in each case with the 'track_to_predict' column as 1 should has predictions for 30 timestamps. Each submission could contain up to 6 modalities where the modality with higher confidence should has smaller index. In other words, modalaity 1 has the highest confidence and modality 6 has the lowest.

Csv files for all scenarios should be packed into **a single zip** file for submission.

[DR_CHN_Merging_ZS0_sub.csv](https://github.com/interaction-dataset/INTERPRET_challenge_single-agent/blob/main/DR_CHN_Merging_ZS0_sub.csv) is an example for submission for the scenario DR_CHN_Merging_ZS0. Note that this example file only contains the first 3 cases with 4 modalities in each case and the input is random number.




## Metrics for Single-Agent Prediction in the ICAPS21/ICCV21 Stage

All metrics are averaged over all cases of all scenarios. The ranking of the single agent prediction is based on **MR**.

### minADE
Minimum Average Displacement Error (minADE) represents the minimum value of the euclid distance averaged by time between the ground truth and modality with the lowest value. The minADE of a single case is calculated as:

![](http://latex.codecogs.com/gif.latex?\\text{minADE}=\\min\\limits_{k\\in\\{1,...,K\\}}\\frac1{T}\\sum\\limits_{t}\\sqrt{(\\hat{x}_{t}-x_{t}^k)^2+(\\hat{y}_{t}-y_{t}^k)^2})
where T is the number of predicted timestamps which is 30 in this challenge, K is the number of modalities in this challenge, $\hat{x}$ and $\hat{y}$ means the ground truth. The final value is averaged over all cases.

### minFDE

Minimum Final Displacement Error (minFDE) represents the minimum value of the euclid distance at the last predicted timestamps between the ground truth and modality with the lowest value. The minFDE of a single case is calculated as:

![](http://latex.codecogs.com/gif.latex?\\text{minFDE}=\\min\\limits_{k\\in\\{1,...,K\\}}\\sqrt{(\\hat{x}_{T}-x_{T}^k)^2+(\\hat{y}_{T}-y_{T}^k)^2})

where T is the number of predicted timestamps which is 30 in this challenge, K is the number of modalities in this challenge, $\hat{x}$ and $\hat{y}$ means the ground truth. The final value is averaged over all cases.


### MR

For a vehicle in a modality of a case, if its prediction at the final timestamp T=30 is out of a given lateral or longitudinal threshold of the ground truth, it is considered as 'miss'. Specifically, we first rotate the groud truth and the prediction according to the ground truth yaw angle at T=30 so that the x-axis is the longitudinal direction and the y-axis is the lateral direction. The lateral threshold is set as 1 meter and the longitudinal is set as a piecewise function based on the velocity v of the ego agent's ground truth at T=30. For the high-speed scenario, the funcition gives a larger longitudinal threshold.


<img src="https://latex.codecogs.com/svg.image?\text{Threshold}_\text{lon}=\left\{\begin{matrix}1&space;&&space;v<1.4m/s&space;\\1&plus;\frac{v-1.4}{v-11}&space;&space;&&space;1.4m/s&space;\leq&space;v&space;\leq&space;11m/s\\2&space;&&space;v&space;\geq&space;11m/s&space;\\\end{matrix}\right." title="\text{Threshold}_\text{lon}=\left\{\begin{matrix}1 & v<1.4m/s \\1+\frac{v-1.4}{v-11} & 1.4m/s \leq v \leq 11m/s\\2 & v \geq 11m/s \\\end{matrix}\right." />

If the lateral or longitudinal distance between the prediction and ground truth at the last timestamp is larger than the cooresponding threshold, this vehicle in this modality of this case is considered as 'miss' - 1. If all modalities of a case are 'miss', we consider the case as 'miss' - 1. Miss Rate (miss rate) calculates the ratio of 'miss' cases over all cases.


## Acknowledgement
Some metrics are inspired by the [Waymo Open Dataset - Motion Prediction Challenge](https://waymo.com/open/challenges/2021/motion-prediction/) and [Argoverse Motion Forecasting Competition](https://eval.ai/web/challenges/challenge-page/454/overview).
