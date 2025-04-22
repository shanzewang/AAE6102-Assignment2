# AAE6102-Assignment2

# Task 1 – Differential GNSS Positioning: Advanced GNSS Techniques for Smartphone Navigation: Comparative Analysis

```
Model: Claude3.7 - Sonnet
Chatting history: https://claude.ai/share/e7a5c995-3a1e-45c0-a660-bf27df5d841d
```

## Introduction

Standard GNSS positioning in smartphones typically achieves only 5-10 meter accuracy. This analysis evaluates four advanced GNSS techniques specifically for smartphone applications, comparing their practical implementation attributes and performance characteristics.

## Comparative Analysis

| Feature | Differential GNSS (DGNSS) | Real-Time Kinematic (RTK) | Precise Point Positioning (PPP) | PPP-RTK |
|---------|---------------------------|---------------------------|--------------------------------|---------|
| **Accuracy** | 1-3 meters | 1-5 centimeters | 10-30 centimeters | 5-10 centimeters |
| **Convergence Time** | Immediate to seconds | Seconds to minutes | 20-30+ minutes | 5-10 minutes |
| **Infrastructure Requirements** | Regional reference stations | Local base stations (10-20 km range) | Global satellite data only | Regional augmentation networks |
| **Hardware Compatibility** | Works with single-frequency receivers | Requires dual-frequency receivers | Optimal with dual-frequency receivers | Optimal with dual-frequency receivers |
| **Data Bandwidth** | Low to moderate | High | Low | Moderate |
| **Coverage Area** | Limited to 50-100 km from stations | Limited to 10-20 km from base stations | Global | Regional to continental |
| **Computational Demands** | Low | Moderate | High | High |
| **Battery Impact** | Low | Moderate to high | High | High |
| **Smartphone Integration Feasibility** | High | Low without external hardware | Moderate | Moderate |
| **Cost Effectiveness** | High | Low | Moderate | Moderate |
| **Urban Performance** | Moderate | Poor to moderate | Poor | Moderate |

## Explanation of Key Comparison Factors

### Accuracy
DGNSS provides corrections enabling meter-range accuracy. RTK technology achieves centimeter-level results through phase measurement analysis. PPP delivers decimeter accuracy by utilizing precise orbital and timing data. The hybrid PPP-RTK approach combines methodologies for reliable centimeter-to-decimeter positioning.

### Convergence Time
DGNSS corrections are applied virtually immediately. RTK requires initialization periods for phase ambiguity resolution. PPP necessitates extended observation windows to achieve optimal accuracy. The hybrid PPP-RTK substantially reduces initialization periods through enhanced regional data integration.

### Hardware Compatibility
Most current mobile devices contain basic receivers suitable for DGNSS implementation. RTK, PPP, and PPP-RTK performance improves significantly with dual-frequency receivers, currently available only in premium mobile devices.

### Computational Demands and Battery Impact
DGNSS requires minimal computational resources. RTK processing demands moderate device capabilities. PPP and PPP-RTK involve sophisticated algorithms that challenge mobile processors and significantly increase energy consumption during continuous operation.

### Smartphone Integration Feasibility
DGNSS can be readily deployed on existing mobile hardware. RTK typically necessitates external hardware components for optimal results. PPP and PPP-RTK integration presents challenges related to mobile antenna limitations and processing constraints.

### Urban Performance
Built environments create substantial challenges for all satellite positioning technologies due to signal obstruction, reflection issues, and limited satellite visibility. DGNSS achieves adequate performance through atmospheric and satellite error correction, though multipath issues remain. RTK performs inconsistently in urban contexts due to carrier phase measurement disruptions and signal discontinuities caused by structures. PPP exhibits the poorest urban performance as its convergence process must restart after interruptions. PPP-RTK delivers improved urban results through enhanced regional augmentation and faster recovery capabilities.

## Analysis by Technique

### Differential GNSS (DGNSS)
DGNSS represents the most immediately viable solution for contemporary mobile implementation. Its compatibility with standard receivers, modest computational demands, and established correction infrastructure make it readily applicable to most devices. The adequate urban performance and meter-level accuracy suffice for numerous consumer navigation requirements.

### Real-Time Kinematic (RTK)
Despite superior accuracy, RTK deployment in mobile devices faces significant implementation barriers. The methodology's vulnerability to signal disruptions, substantial data requirements, and general dependence on external hardware limit its practical application for mainstream mobile navigation.

### Precise Point Positioning (PPP)
PPP eliminates dependence on localized reference infrastructure but suffers from extended initialization periods and poor performance in urban settings. These limitations, coupled with intensive computational requirements, render it currently unsuitable for typical mobile navigation scenarios requiring immediate positioning.

### PPP-RTK
This integrated approach represents a promising future direction, offering reduced initialization times compared to standard PPP and expanded coverage relative to RTK. As dual-frequency receivers become commonplace in mobile devices, the hybrid PPP-RTK methodology may provide an optimal balance of accuracy, coverage, and performance.

## Conclusion

For current smartphone navigation applications, DGNSS represents the most practical solution, balancing improved accuracy with feasible implementation requirements. As smartphone hardware evolves, PPP-RTK shows the greatest promise for future high-precision navigation applications, particularly as dual-frequency receivers become standard in consumer devices.


# Task 2 - GNSS in Urban Areas

## Fundamentals of Skymask Application

The analyzed skymask information comprises correlations between directional measurements—specifically the azimuthal coordinates (AZ values ranging from 1-360 degrees) and their associated minimum elevation thresholds (EL values) that permit direct visibility. When employing Standard Least-squares Estimation techniques, incoming satellite transmissions can be evaluated by determining their specific azimuthal position, then assessing whether the corresponding elevation exceeds the minimum threshold established in the skymask dataset. Transmissions with elevations falling below these thresholds are considered obstructed and subsequently excluded from positioning calculations. Should fewer than four satellites remain viable after this filtering procedure, position determination for that particular time point becomes unfeasible.

## Processing Methodology Description

The procedural framework for implementing skymask filtering operates as follows: Initially, the system processes a collection of GNSS observations with no qualifying line-of-sight transmissions recorded. For each detected satellite, the system calculates its elevation parameter and determines its azimuthal orientation. Using this azimuth value, the corresponding minimum elevation requirement is retrieved from the skymask dataset. The system then evaluates whether the satellite's actual elevation falls below this threshold value. If so, the transmission is classified as non-line-of-sight and excluded; otherwise, it increases the running count of viable observations. This process repeats for all detected satellites. Upon completion, if the total of qualifying transmissions falls below four, the positioning attempt is abandoned for that epoch; otherwise, the qualifying satellite data proceeds to the positioning computation stage.

## Performance Analysis Results

Analysis of urban environment data spanning 839 distinct measurement epochs revealed notable differences between standard estimation techniques and those incorporating skymask filtering. The implementation of skymask filtering demonstrated significant improvements in positioning precision by eliminating low-quality non-line-of-sight transmissions. However, numerical assessment revealed a complex impact on overall accuracy metrics. The standard estimation technique produced a three-dimensional root-mean-square error of 85.13 meters with a standard deviation of 35.33 meters across all 839 measurement epochs. In contrast, the skymask-enhanced approach yielded a higher root-mean-square error of 100.36 meters but achieved a slightly improved standard deviation of 33.18 meters, also maintaining complete availability across all 839 epochs.

This outcome suggests a notable tradeoff: while filtering enhances precision through signal quality control, it potentially compromises accuracy by reducing the geometric diversity of satellite signals, particularly in the vertical dimension. The reduced satellite count after filtering, while eliminating problematic signals, also diminishes the vertical positioning geometry, potentially explaining the deterioration in three-dimensional positioning performance despite improved precision metrics. Theoretically, this filtering approach could result in insufficient satellite availability for some measurement periods, though in this particular dataset, solutions remained available for all epochs regardless of methodology.

The result graph is shown below,
![image](https://github.com/shanzewang/AAE6102-Assignment2/blob/main/code/results_graph/latitude_longitude.png)


# Task 3 - GPS RAIM (Receiver Autonomous Integrity Monitoring)

## Dataset and Methodology Overview

This analysis employs the "OpenSky" dataset containing 926 distinct temporal measurements. Each measurement epoch consistently records 9 satellite signal receptions. The methodology implements RAIM protocols which first perform fault detection, followed by isolation procedures. Upon identifying a single erroneous measurement, the system isolates this specific observation and recalculates positioning parameters.

## Implementation Specifications

For Weighted Least Squares positioning implementation, modify `settings.sys.ls_type = 1` in the "42.m" file. The weighted RAIM algorithm integration occurs within lines 154-189 of "calcPosLSE.m", utilizing supporting functional components "chi2_detector.m" and "compute_PL.m".

## Mathematical Framework

The weighted position estimation $\boldsymbol{X}$ is determined through the expression:

$\boldsymbol{X} = (G^T W G)^{-1} G^T W \boldsymbol{Y}, \tag{1}$

with the WSSE (Weighted Sum of Squared Errors) statistical metric formulated as:

$WSSE = \sqrt{\boldsymbol{Y}^T W (I - P) \boldsymbol{Y}}, \tag{2}$

where the detection threshold $T$ is established by:

$T(N, P_{FA}) = \sqrt{Q_{\chi^2, N-4}(1 - P_{FA})}, \tag{3}$

with $Q_{\chi^2, N-4}(\cdot)$ representing the quantile function for Chi-square distributions having $N-4$ degrees of freedom. The three-dimensional Protection slope parameter for each satellite $i$ is calculated using:

$\text{Pslope} = \frac{\sqrt{(K^2_{1,i} + K^2_{2,i} + K^2_{3,i})}}{\sqrt{W_{ii}(1 - P_{ii})}}. \tag{4}$

Integration of equations $(3)$ and $(4)$ enables computation of the three-dimensional Protection Level (PL):

$\text{PL} = max[\text{Pslope}] T(N, P_{FA}) + k(P_{MD})\sigma, \tag{5}$

where $\sigma = 3\text{m}$, and $k(P_{MD}) = Q_N (1 - \frac{P_{MD}}{2})$ with $Q_N(\cdot)$ denoting the standard normal distribution quantile function.

## Performance Assessment

Both conventional and weighted RAIM frameworks were evaluated against OLS and WLS positioning outputs respectively. The traditional RAIM implementation revealed certain epochs generating test statistics (represented in performance charts) exceeding calculated thresholds, indicating potential measurement anomalies. Further investigation during isolation phases consistently identified two potentially faulty measurements among the nine received signals for these problematic epochs. This situation prevented successful isolation of individual faults, necessitating positioning abandonment during affected epochs with corresponding suspension of Protection Level calculations. All successfully calculated PLs remained below the established Alert Limit of 50 meters, confirming the effectiveness of the developed RAIM methodology in identifying and excluding compromised or substandard measurements.

The weighted RAIM implementation demonstrated that test statistics across all 926 epochs remained below calculated thresholds. The weighting mechanism eliminated detection of potentially faulty measurements, thereby circumventing isolation procedures. Protection Levels were successfully calculated for all measurement epochs, with verification charts confirming all PL values remained within acceptable Alert Limits.

The result graphs are shown below,
![image](https://github.com/shanzewang/AAE6102-Assignment2/blob/main/code/results_graph/OLS.png)

![image](https://github.com/shanzewang/AAE6102-Assignment2/blob/main/code/results_graph/WLS.png)

# Task 4 – LEO Satellites for Navigation: Challenges in Utilizing LEO Communication Satellites for Navigation: Critical Analysis of System Architecture Limitations

```
Model: Claude3.7 - Sonnet
Chatting history: https://claude.ai/share/e7a5c995-3a1e-45c0-a660-bf27df5d841d
```

## Introduction

The integration of Low Earth Orbit (LEO) communication satellite constellations into navigation frameworks presents a paradigm shift from traditional Medium Earth Orbit (MEO) Global Navigation Satellite Systems (GNSS). While LEO architectures offer theoretical advantages including enhanced signal strength, improved geometric dilution of precision, and reduced propagation delays, they introduce significant technical and operational challenges that require rigorous examination. This analysis provides a systematic assessment of the primary obstacles in five critical domains that constrain LEO-based navigation capabilities.

## Orbit and Clock Determination Challenges

The fundamental precision of any satellite navigation system depends on accurate orbit determination and clock synchronization. LEO satellites present unique challenges in this domain due to their complex orbital dynamics. The lower altitude subjects these vehicles to significant non-gravitational perturbations, including atmospheric drag that varies with solar activity and geomagnetic conditions. These perturbations induce accelerations that can reach 10^-6 m/s² — orders of magnitude greater than those experienced by MEO satellites.

The precise orbit determination (POD) of LEO satellites requires sophisticated force modeling incorporating higher-order gravitational terms, solid Earth and ocean tides, and relativistic effects. Additionally, the rapid orbital velocity necessitates frequent updates to ephemeris data, as prediction errors propagate more quickly than in MEO systems. Studies indicate that without compensatory measures, LEO orbit prediction errors can exceed 10 meters after just 6 hours, compared to similar errors occurring after 24+ hours for GPS satellites.

Clock stability presents an equally significant challenge. Communication satellites typically employ temperature-compensated crystal oscillators (TCXOs) or oven-controlled crystal oscillators (OCXOs) rather than the atomic frequency standards used in navigation systems. These oscillators exhibit frequency stability (Allan deviation) of 10^-9 to 10^-10 over one day, compared to 10^-14 for navigation-grade atomic clocks. This deficiency creates timing uncertainties that translate directly to ranging errors, necessitating sophisticated clock modeling and frequent synchronization with ground-based time references.

## Integration with Existing GNSS Infrastructure

The incorporation of LEO navigation capabilities into the established GNSS ecosystem presents significant interoperability challenges. Existing receivers are designed for the signal characteristics, orbital dynamics, and navigation message structures of MEO constellations. Fundamental architectural differences include:

The reference frame implementation requires careful consideration, as LEO systems must maintain compatibility with international terrestrial reference frames to ensure seamless integration with existing GNSS. Differential biases between LEO and MEO systems must be resolved to centimeter-level accuracy to enable multi-constellation solutions without degrading overall performance.

Additionally, time system synchronization between LEO constellations and established GNSS creates systematic challenges. Each existing GNSS maintains its own system time, with complex relationships to Universal Coordinated Time (UTC). Introducing LEO timing systems requires nanosecond-level inter-system bias determination and compensation to prevent solution degradation in combined navigation frameworks.

## Coverage and Constellation Architecture Requirements

LEO navigation constellations face fundamental coverage limitations due to the restricted visibility footprint of each satellite. A LEO satellite at 1,200 km altitude has a visibility radius of approximately 3,900 km, compared to 13,600 km for a GPS satellite. This restricted footprint necessitates substantially more satellites to maintain comparable coverage metrics.

Quantitative analysis indicates that achieving continuous global navigation service with position dilution of precision (PDOP) values below 6 would require a minimum of 80-120 LEO satellites, depending on orbital parameters and constellation design. Furthermore, achieving the four-satellite minimum visibility criterion with 99.9% availability globally necessitates approximately 160-200 satellites across multiple orbital planes.

The optimization of LEO constellation design presents a multi-objective problem balancing coverage, geometric diversity, launch constraints, and collision avoidance requirements. Walker constellations and more complex asymmetric designs must be evaluated against these competing objectives, with particular attention to coverage at high latitudes where visibility gaps are most pronounced.

## Signal Diversity and Processing Complexity

The signal environment for LEO navigation differs substantially from traditional GNSS. LEO communication satellites transmit signals with characteristics optimized for data throughput rather than positioning performance. Adapting these signals for navigation purposes introduces several technical complications:

The Doppler shift range for LEO satellites can exceed ±45 kHz, requiring significantly wider bandwidth acquisition algorithms and more complex tracking loops in user receivers. This increased processing burden translates to higher power consumption and computational requirements, challenging the implementation in resource-constrained devices.

Signal structures must be redesigned to incorporate ranging codes with suitable autocorrelation properties, navigation data with appropriate forward error correction, and modulation schemes resistant to multipath and interference. This redesign must occur within the constraints of existing communication payloads, creating inevitable performance compromises.

## Security Vulnerabilities and Mitigation Strategies

LEO navigation signals present a modified threat profile regarding jamming and spoofing vulnerabilities. While the higher signal strength provides approximately 10-15 dB improvement in jamming resistance compared to MEO systems, the concentration of satellites in view creates new attack vectors.

The rapidly changing satellite geometry complicates traditional anti-spoofing techniques based on consistency checking. Moreover, the potentially commercial nature of LEO constellations may limit the implementation of military-grade security features such as encrypted signals. The shorter signal propagation distance reduces the effectiveness of certain cryptographic authentication schemes due to the reduced time available for signal validation before processing.

Systematic implementation of navigation message authentication (NMA) and signal authentication sequences would be essential, requiring dedicated security modules within the communication payload that may conflict with primary mission objectives.

## Conclusion

The utilization of LEO communication satellites for navigation purposes represents a technologically ambitious objective that must overcome substantial challenges in orbit determination precision, system integration, constellation architecture, signal processing complexity, and security hardening. While these obstacles are not insurmountable, they require dedicated engineering solutions and potentially significant modifications to existing and planned LEO communication systems. The most promising approach may involve complementary integration with traditional GNSS rather than wholesale replacement, leveraging the strengths of each orbital regime while minimizing their respective limitations.


# Task5 - GNSS Remote Sensing: The Impact of GNSS Radio Occultation in Remote Sensing Applications

```
Model: Claude3.7 - Sonnet
Chatting history: https://claude.ai/share/e7a5c995-3a1e-45c0-a660-bf27df5d841d
```

## Introduction: Principles and Emergence of GNSS-RO

Global Navigation Satellite System Radio Occultation (GNSS-RO) has emerged as a revolutionary atmospheric remote sensing technique over the past two decades. This method utilizes the refraction of GNSS signals as they traverse the Earth's atmosphere to derive vertical profiles of atmospheric properties with unprecedented accuracy and precision. Unlike traditional remote sensing approaches, GNSS-RO provides all-weather capability, global coverage, high vertical resolution, and SI-traceability without requiring instrument calibration.

The technique was first demonstrated in 1995 with the GPS/MET experiment and has since evolved from an experimental approach to an operational observing system making significant contributions to numerical weather prediction (NWP) and climate monitoring. The fundamental principle leverages the bending of radio signals from GNSS constellations (GPS, GLONASS, Galileo, BeiDou) as they pass through the atmosphere's varying density layers before being detected by receivers on low Earth orbit (LEO) satellites.

## Technical Foundations: Signal Propagation Physics and Measurement Principles

GNSS-RO functions on the basis of atmospheric refractivity's effect on electromagnetic wave propagation. As GNSS signals travel through the atmosphere, they encounter gradients in refractivity primarily influenced by temperature, pressure, and water vapor content. This causes the signals to follow curved paths rather than straight lines, with the degree of bending directly related to the vertical refractivity gradient.

The measurement process occurs when a GNSS satellite rises or sets relative to a LEO satellite, creating an "occultation event." During this geometry, the LEO satellite receives signals that have traversed progressively deeper atmospheric layers. By precisely measuring the phase delay and Doppler shift of these signals with millimeter-level precision, the bending angle profile can be determined. Through the application of the Abel transform under the assumption of spherical symmetry, these bending angles are converted to refractivity profiles, which are subsequently processed to retrieve temperature profiles in the upper troposphere and stratosphere (where water vapor content is negligible) and combined temperature-humidity profiles in the lower troposphere.

The technique exhibits remarkable vertical resolution of approximately 200-300 meters in the troposphere, gradually decreasing to 1-1.5 kilometers in the stratosphere. This resolution capability substantially exceeds that of other satellite-based sounding systems, particularly in the vertical dimension.

## Major Applications and Contributions to Atmospheric Science

The COSMIC/FORMOSAT-3 mission, launched in 2006 with six microsatellites, marked the first operational GNSS-RO constellation, providing approximately 2,000 daily profiles globally. This mission demonstrated GNSS-RO's transformative impact on NWP, with studies showing forecast skill improvements of 6-12 hours in the Southern Hemisphere—a region historically undersampled by conventional observations. Quantitatively, COSMIC data reduced 24-hour forecast errors by 10-15% in mid-tropospheric temperature fields and improved 500 hPa geopotential height predictions by up to 6 meters in mid-latitudes.

The subsequent COSMIC-2/FORMOSAT-7 constellation, launched in 2019, enhanced these capabilities with advanced GNSS receivers capturing roughly 5,000 daily profiles concentrated in the tropics. This improved tropical cyclone track forecasts by 6-12 hours and intensity predictions by approximately 15%, addressing a longstanding challenge in tropical meteorology.

Beyond weather forecasting, GNSS-RO has made significant contributions to climate science by providing long-term, stable measurements of atmospheric parameters unaffected by calibration drift—a persistent issue with radiometer-based systems. Studies utilizing multi-year GNSS-RO datasets have documented stratospheric cooling (-0.1 to -0.3 K/decade) and tropospheric warming (+0.1 to +0.4 K/decade) patterns with unprecedented vertical detail, serving as critical benchmarks for climate model evaluation.

The technique's all-weather capability has enabled breakthrough research in atmospheric gravity waves, tropopause structure dynamics, and atmospheric boundary layer characterization—phenomena difficult to observe systematically with traditional methods.

## Current Limitations and Technical Challenges

Despite its numerous advantages, GNSS-RO faces several limitations. The technique struggles in the lower troposphere, particularly in humid tropical regions, where sharp moisture gradients can cause signal multipath effects and superrefraction, complicating the retrieval process. Current processing algorithms show reduced accuracy below 2-3 kilometers in such environments.

Spatial and temporal coverage remains suboptimal for certain applications requiring high-frequency observations. The opportunistic nature of occultation events creates an uneven global distribution of measurements, with most current constellations providing insufficient density for mesoscale weather monitoring or diurnal cycle studies.

The assumption of spherical symmetry in standard retrieval algorithms introduces errors in regions with strong horizontal gradients, such as near weather fronts or the jet stream, potentially limiting accuracy in precisely the atmospheric regions of greatest meteorological interest.

## Future Developments and Emerging Applications

The future of GNSS-RO appears promising with several technological advancements and mission concepts under development. Next-generation receivers capable of tracking signals from multiple GNSS constellations simultaneously will substantially increase observation density. Advanced signal processing techniques, including phase-matching methods and wave optics approaches, are improving retrievals in the challenging lower troposphere.

Commercial providers are entering the GNSS-RO domain, with companies like Spire Global and GeoOptics deploying CubeSat constellations that augment coverage from government missions. These commercial initiatives may eventually enable hourly global refresh rates, approaching the temporal resolution needed for tracking rapidly evolving weather systems.

Innovative applications emerging in research include using GNSS-RO for characterizing atmospheric turbulence, monitoring volcanic ash plumes, and potentially detecting severe weather precursors through identification of specific refractivity signatures associated with intense convection.

## Conclusion

GNSS Radio Occultation has transformed from an experimental technique to an essential component of the global Earth observation system in just two decades. Its unique combination of high vertical resolution, all-weather capability, global coverage, and calibration-free stability fills critical observational gaps left by traditional remote sensing systems.

The quantifiable improvements in weather prediction and climate monitoring demonstrate GNSS-RO's exceptional scientific value per investment cost. As the constellation infrastructure expands and retrieval methodologies advance, GNSS-RO will continue to enhance our understanding of atmospheric processes while improving services dependent on accurate weather and climate information.

Perhaps most significantly, GNSS-RO exemplifies how repurposing existing infrastructure (navigation satellites) with innovative measurement concepts can yield profound advances in Earth observation capabilities, providing a model for future remote sensing innovations.
