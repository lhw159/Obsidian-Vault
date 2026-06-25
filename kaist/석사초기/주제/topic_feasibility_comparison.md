## TL;DR

All three topics are feasible with kinematics-only biplanar fluoroscopy data. Contact location, tibial translation/rotation, and the screw-home mechanism are determined purely from bone geometry and 6-DOF pose — no ground reaction force or contact force data is needed.

----

## Tibiofemoral contact location analysis

Condylar contact point (CP) analysis is fundamentally a geometry-based operation: once the 6-DOF pose of each bone is known from fluoroscopic tracking, the closest point on opposing articular surfaces is computed directly from the bone models. No force data enters the calculation at any stage.

The literature strongly supports this approach. Studies using biplane X-ray imaging combined with CT- or MRI-derived bone models compute CP locations throughout the gait cycle without any GRF input [1] [2] [3]. The method reduces to projecting the femoral condylar geometry onto the tibial plateau surface at each tracked pose [4]. Cartilage contact regions and thickness distributions have been mapped across six functional activities — including downhill walking — using exactly this pipeline [3]. Even early single-plane fluoroscopy studies determined medial and lateral condylar contact paths and lift-off solely from shape-matching kinematics [5] [6]. A comprehensive review of fluoroscopy in osteoarthritis confirmed that cartilage-to-cartilage contact locations during gait are estimated entirely from kinematics-derived bone positions [7]. Slip velocity, which combines CP location with joint angular velocity, has similarly been calculated for 75 TKA patients across five activities using only 6-DOF kinematics [2].

| Paper                      | Method                                           | Force data required? | Population                           |
| -------------------------- | ------------------------------------------------ | -------------------- | ------------------------------------ |
| Guan et al. 2017 [1]       | Biplane fluoroscopy + CT; CP on Z-axis of tibia  | No                   | TKA, overground/treadmill            |
| Chen et al. 2016 [4]       | Fluoroscopy; closest-point on opponent surface   | No                   | TKA                                  |
| Dennis et al. 2003 [5] [6] | Single-plane fluoroscopy; shape matching         | No                   | TKA (PS and CR)                      |
| Thomeer et al. [3]         | Biplane X-ray + MRI; contact regions + thickness | No                   | Healthy, 6 activities incl. downhill |
| Guan et al. 2024 [2]       | Mobile biplane X-ray; CP + slip velocity         | No                   | TKA, 5 activities                    |
| Dur et al. 2024 [7]        | Fluoroscopy review; cartilage contact in OA      | No                   | OA, gait                             |

**Novelty for HTO cohort:** virtually all CP literature focuses on TKA or healthy knees. Applying geometry-based CP analysis to native bone geometry in HTO patients pre/post-operatively during both level and slope walking is highly novel, with slope walking essentially unstudied in this population.

----

## Tibial translation and rotation during gait

Anterior-posterior (AP) tibial translation and internal-external (IE) tibial rotation are direct outputs of 6-DOF fluoroscopic tracking; they require no force measurements whatsoever. The technique has been validated at sub-millimeter and sub-degree accuracy using contour-based biplane fluoroscopy at multiple walking speeds [8].

Several fluoroscopy studies have characterized AP translation and IE rotation across knee pathologies during gait:

- **ACL-deficient knees** showed significantly increased anterior tibial translation during stance compared to the intact contralateral side, measured with dual fluoroscopy [9].
- **ACL-reconstructed knees** that failed return-to-sport tests had larger anterior tibial translation (3.64 vs. 3.03 mm peak), greater lateral tibial displacement, and larger external rotation angle during the support phase, all measured kinematically [10].
- **PCL-insufficient knees** showed substantially increased passive laxity (15.5 ± 5.9 mm vs. 3.7 ± 1.9 mm AP translation), yet the range of AP and rotational motion during level walking was not significantly different from the contralateral side [11].
- **Obese individuals with knee pain** exhibited persistent anterior tibial translation during stance and reduced flexion ROM, quantified with fluoroscopic 6-DOF kinematics [12].
- **Healthy knees** during biplanar fluoroscopy treadmill walking show that tibial anterior translation is linearly coupled to flexion angle (stance: y = 0.31x − 0.54), providing normative coupling functions [13].
- **HTO patients** (medial knee OA) have been assessed for 3D tibiofemoral kinematics pre/post-operatively during gait, with tibial rotation documented using a model-based matching approach [14].

**Novelty for HTO cohort:** Takemae et al. [14] studied HTO kinematics but used single-plane fluoroscopy; biplanar fluoroscopy provides full 6-DOF data with superior accuracy. Critically, no published study has characterized AP translation and IE rotation during slope walking in HTO patients pre/post-operatively, representing a clear gap.

----

## Screw-home mechanism in HTO

The screw-home mechanism (SHM) — coupled tibial external rotation as the knee extends toward terminal extension — is defined and quantified entirely from the IE rotation and flexion/extension time series. It is a purely kinematic phenomenon and requires no force data.

Fluoroscopy studies have examined the SHM in multiple contexts:

- **Level walking in normal knees:** video fluoroscopy demonstrated the SHM operating during gait, with tibial external rotation occurring in the final degrees of extension [15]. Biplanar fluoroscopy during treadmill walking in healthy subjects confirmed a quadratic coupling of tibial external rotation to flexion angle, with lateral pivot motion characterizing the terminal extension phase [13].
- **Single-plane fluoroscopy during active extension** documented the screw-home pattern as a longer path of the lateral condyle relative to the medial condyle during knee extension [16].
- **TKA designs:** fluoroscopy studies show that some designs reproduce a screw-home pattern (femoral internal rotation in full extension) while others do not [17], and a bicruciate-retaining TKA reproduced the natural knee's SHM pattern in a step-up task [18]. In mobile-bearing TKA, the average screw-home rotation was +0.5° with a maximum of 9.6°, measured via videofluoroscopy [19].
- **HTO specifically:** Takemae et al. [14] is the key paper — it directly studied SHM before and after HTO for medial knee OA during gait. Two SHM patterns were observed in the early stance phase, and the study examined changes in external tibial rotation magnitude post-HTO. A related proximal tibial osteotomy study (hemicallotasis, distal tuberosity variant) found tibial external rotation of 10° ± 3° pre-operatively and 8° ± 4° post-operatively during stair ascent, with no statistically significant change [20].

**HTO effect on SHM:** The existing evidence from [14] and [20] suggests the SHM pattern is preserved after osteotomy, but sample sizes are small (6–13 patients) and activities tested are limited to level gait or stair ascent. **Novelty for HTO cohort:** no study has used biplanar fluoroscopy (with its superior 6-DOF accuracy over single-plane methods) to characterize the SHM during slope walking before and after HTO. The effect of the altered coronal alignment and load redistribution from HTO on SHM coupling during inclined locomotion — a mechanically distinct loading condition — has not been described.

----

## Overall feasibility and novelty summary

All three topics are purely kinematic; none require GRF or contact force data.

| Topic | Force data needed? | Key supporting papers | HTO/slope walking novelty |
|---|---|---|---|
| Tibiofemoral CP location | No — geometry + kinematics only | [1] [4] [5] [6] [2] [7] [3] | High: no CP study in HTO native knee with slope walking |
| AP translation / IE rotation | No — direct 6-DOF output | [9] [11] [10] [12] [14] [13] | High: slope walking pre/post HTO not studied with biplanar fluoroscopy |
| Screw-home mechanism | No — IE rotation vs. flexion angle | [15] [19] [14] [17] [20] [18] [13] [16] | High: biplanar fluoroscopy + slope walking in HTO is unstudied |

The 9-patient HTO pre/post biplanar fluoroscopy dataset with both level and slope walking conditions is well-positioned to make original contributions on all three fronts, with slope walking being the most consistently unexplored condition across all three topics.

## References

[1]S. Guan, H. Gray, A. G. Schache, J. A. Feller, R. de Steiger, and M. G. Pandy, “In vivo six‐degree‐of‐freedom knee‐joint kinematics in overground and treadmill walking following total knee arthroplasty,” Journal of Orthopaedic Research, Aug. 2017, doi: 10.1002/JOR.23466.

[2]S. Guan, R. Dumas, and M. G. Pandy, “Tibiofemoral Slip Velocity in Total Knee Arthroplasty is Design-Invariant but Activity-Dependent,” Annals of Biomedical Engineering, Mar. 2024, doi: 10.1007/s10439-024-03490-4.

[3]L. T. Thomeer, S. Guan, H. A. Gray, and M. G. Pandy, “Articular Contact Motion at the Knee During Daily Activities”, doi: 10.1002/jor.25222.

[4]Z. Chen, Z. Jin, Z. Jin, and Z. Jin, “Prediction of in-vivo kinematics and contact track of total knee arthroplasty during walking,” Biosurface and Biotribology, Sept. 2016, doi: 10.1016/J.BSBT.2016.08.002.

[5]D. A. Dennis, R. D. Komistek, and M. R. Mahfouz, “In vivo fluoroscopic analysis of fixed-bearing total knee replacements : Knee kinematics and total knee replacement design,” Clinical Orthopaedics and Related Research, no. 410, pp. 114–130, Jan. 2003.

[6]D. A. Dennis, R. D. Komistek, and M. R. Mahfouz, “In vivo fluoroscopic analysis of fixed-bearing total knee replacements.,” Clinical Orthopaedics and Related Research, vol. 410, no. 410, pp. 114–130, May 2003, doi: 10.1097/01.BLO.0000062385.79828.72.

[7]N. B. J. Dur, M. Wesseling, E. M. Macri, and J. Runhaar, “Fluoroscopy: taking a closer look at joint motion in osteoarthritis,” Osteoarthritis Imaging, June 2024, doi: 10.1016/j.ostima.2024.100240.

[8]J. E. Giphart, C. A. Zirker, C. A. Myers, W. W. Pennington, and R. F. LaPrade, “Accuracy of a contour-based biplane fluoroscopy technique for tracking knee joint kinematics of different speeds,” Journal of Biomechanics, vol. 45, no. 16, pp. 2935–2938, Nov. 2012, doi: 10.1016/J.JBIOMECH.2012.08.045.

[9]C.-H. Chen, J.-S. Li, A. Hosseini, H. R. Gadikota, T. J. Gill, and G. Li, “Anteroposterior Stability of the Knee during the Stance Phase of Gait after Anterior Cruciate Ligament Deficiency,” Gait & Posture, vol. 35, no. 3, pp. 467–471, Mar. 2012, doi: 10.1016/J.GAITPOST.2011.11.009.

[10]T.-E. Zhou, Y. Xu, L. Zhou, S. Y. Wang, S. Wang, and W. Xu, “Multi-planar instability, laxity and reduced knee flexion during the support phase of walking are determinants of return to sports,” Frontiers in Bioengineering and Biotechnology, vol. 10, Nov. 2022, doi: 10.3389/fbioe.2022.1047135.

[11]S. Oehme et al., “PCL insufficient patients with increased translational and rotational passive knee joint laxity have no increased range of anterior–posterior and rotational tibiofemoral motion during level walking,” Dental science reports, vol. 12, no. 1, Aug. 2022, doi: 10.1038/s41598-022-17328-3.

[12]J.-S. Li, T.-Y. Tsai, D. T. Felson, G. Li, and C. L. Lewis, “Six degree-of-freedom knee joint kinematics in obese individuals with knee pain during gait.,” PLOS ONE, vol. 12, no. 3, Mar. 2017, doi: 10.1371/JOURNAL.PONE.0174663.

[13]Y.-J. Koo and S. Koo, “Three-Dimensional Kinematic Coupling of the Healthy Knee During Treadmill Walking,” Journal of Biomechanical Engineering-transactions of The Asme, vol. 141, no. 8, p. 081012, Aug. 2019, doi: 10.1115/1.4043562.

[14]T. Takemae, G. Omori, K. Nishino, K. Terajima, Y. Koga, and N. Endo, “Three-dimensional knee motion before and after high tibial osteotomy for medial knee osteoarthritis,” Journal of Orthopaedic Science, Nov. 2006, doi: 10.1007/S00776-006-1066-1.

[15]M. Foresti, H. Gerber, and E. Stuessi, “Automated moving fluoroscope: a novel method to acquire video fluoroscopic data during level walking,” Journal of Biomechanics, Jan. 2006, doi: 10.1016/S0021-9290(06)83332-4.

[16]T.-W. Lu, T.-Y. Tsai, M.-Y. Kuo, M.-Y. Kuo, H.-C. Hsu, and H.-L. Chen, “In vivo three-dimensional kinematics of the normal knee during active extension under unloaded and loaded conditions using single-plane fluoroscopy,” Medical Engineering & Physics, Oct. 2008, doi: 10.1016/J.MEDENGPHY.2008.03.001.

[17]F. Zambianchi et al., “Changes in total knee arthroplasty design affect in-vivo kinematics in a redesigned total knee system: a fluoroscopy study,” Clinical Biomechanics, May 2018, doi: 10.1016/J.CLINBIOMECH.2018.03.014.

[18]S. V. Agapov et al., “Fluoroscopic and radiostereometric analysis of a bicruciate-retaining versus a posterior cruciate-retaining total knee arthroplasty: a randomized controlled trial,” vol. 105-B, no. 1, pp. 35–46, Jan. 2023, doi: 10.1302/0301-620x.105b1.bjj-2022-0465.r2.

[19]J. B. Stiehl, D. A. Dennis, R. D. Komistek, and H. S. Crane, “In vivo determination of condylar lift-off and screw-home in a mobile-bearing total knee arthroplasty.,” Journal of Arthroplasty, vol. 14, no. 3, pp. 293–299, Apr. 1999, doi: 10.1016/S0883-5403(99)90054-2.

[20]N. Okamoto, E. Nakamura, T. Masuda, S. Hisanaga, and T. Miyamoto, “In vivo Knee Kinematics during Step-up Activity after Medial Opening Wedge Distal Tuberosity Osteotomy of the Proximal Tibia with Hemicallotasis,” Aug. 2023, doi: 10.21203/rs.3.rs-3233581/v1.