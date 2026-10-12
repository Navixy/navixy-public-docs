---
description: Map the motion ignition input for Suntech ST4315, ST4410, and ST4215 models in Navixy. Shows how physical inputs map to platform input channels.
---

# Mapping the ignition motion input for Suntech devices in Navixy

Suntech devices are telemetry equipment used for tracking light and heavy units. It has multiple interfaces for serial equipment connections and various I/O, allowing flexibility in communication with external accessories.

Currently, the efficient use of the inputs on the device means optimizing resources, as this can reduce wiring costs or additional use of sensors while taking advantage of the full functionality of the device. For instance, the ST4315 device has two physical inputs, but with the option of being configured with motion ignition, allowing both inputs to be used. However, it may not be configured this way, connecting one input to the ignition and the other to a sensor.&#x20;

This technical document will explain the configuration of the Suntech ST4315 device using motion ignition and the relationship of the number of inputs with Navixy platform, which will be replicated for additional Suntech branded models.

The device configuration, using Syntrack software, for motion ignition, would be configured as this:&#x20;

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcYH--FS16PqkKY1FUzKLEsih6x3XFm6sYlKLwNnK-Amt-LL2mR9Tdz0Ps1Pe-sxvmVoAcTXhnlicK6QeVommF2UH33s-MqWIOqD0Nyg4yMo965j3DpqQ2C5qTVbyBMqunzg-B9Atqd29LzJz5wW9--495-?key=bZs4OqgxBiGnj_EN9_Rr1w)

Inputs are set:

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdpoH4DjaynqVujHAS-uSzxf5pP8RPllmzEhgZkkMAhMrz7PEOjTwhO5jaGXt_LBlfWi7LIb_LK2dTixxRXz1gV2N7ZYdyRh7sASrsM2mH6sidFgG7l38OV8WHWOI5PDxqIertVYKfYGvfQFL5GSytMqTAm?key=bZs4OqgxBiGnj_EN9_Rr1w)

Ignition status is defined in the **Ignition** tab. Inputs are set on the input tab, as it is shown above the first input is set as the panic button and second input is set as the door sensor.&#x20;

During the activation of the device in the Navixy platform, in the device blocks tool, three inputs are displayed, but the ST4315 device only has 2 physical inputs, which may cause confusion and misunderstanding among customers, as they will surely report that the integration has been incorrect.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcrx2J_fXjLfrbYWZ-y_E-uhsw2Q7Rs82C8e6SbXEZjxdKA2pdY7lFETiePJtFqIVEerwRLl4IujJTcsDV7otD2Vk0N7OztzBsRLE_62xHtI3wNGQsaRmU8nbCWqPnQlnCvQ9FGTj7oKfoYKWqyWo_vJQ0?key=bZs4OqgxBiGnj_EN9_Rr1w)

However, using Air Console in the Navixy platform, you can visualize the input data sent by the device.&#x20;

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXc_rqbOr66Y4jIMKeCIHMBdDmo_4t9hUnkXPrZh7idgCfdStuBmdiTNmQFIAYZT0_mARCCra7jYuMe1CAa1dbKy7ZovGU1Lkt9gpuAKTioL3golhx8L1BNKZnAV8BZ7XSkmVzcIr0KdAnpmShIMLBvdt0sV?key=bZs4OqgxBiGnj_EN9_Rr1w)

Based on Suntech's protocol the three initial bits are the input status bits:

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfak3KPedsXDCFX8DHPFLUC1t_aBuqLbdB7glQPT_305EudL66aailkyq4GvV9UR3Y3BfKYegr8OxLYIbf6erirftuARohxk9brM5TtvU7KiJDxFE4qFMGLPdy0MhBHyxcRjFsR6W2GOn4GQLzQl8oZDhVm?key=bZs4OqgxBiGnj_EN9_Rr1w)

In Air Console, we can make sure which bits are active and how we would expect to see them in Navixy UI.&#x20;

By creating the digital sensors in device settings under the previous configuration, it is shown the change of state of each of these.&#x20;

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdb7wcp__P7QxdqWh_YpjoA_ZedRXWPbW9vMTSxIHBoeM-BHc6ATifUobs6b25Jyh_OZ-Ax4JKcI4VK-43hZpFOVIsdoZWCed1SEm4KZpBfuL8Zcme-xvNJa_kOsgDjqApvYWJK8z6Ye9MVg5UKBppbP5Q?key=bZs4OqgxBiGnj_EN9_Rr1w)

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeiGSAL4AlCH3rmX-wzV9jqDvxstdv2PhXovrLdS2vnPzCfT786777Cmh9EbyeiKZ75DshTBqJyXa5tr9XJ-_aVbu-mDjzyLlv5KRswUMaaxPVaqydEWvMY1JkKdcpYHLjDQ1-F8eVUphZjl_RfFStLFY4j?key=bZs4OqgxBiGnj_EN9_Rr1w)

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXd9Mo3bl5I50IeHVUSOIVDt4Cqj5KmcoP_1uma_SvvMaKPPNb8uE8_GVAuBN44FXj754G-N35U0IHmtpK6bXFmttk-lNezjts8JdgdTJFM1pHbJyWVOkJHOXWtmPVVY8-5hvbJANkzf49DoRaGJNKFYWZO7?key=bZs4OqgxBiGnj_EN9_Rr1w)

Now let’s see the ignition configuration through the physical connection to input 1 (IN1).

In the Syntrack software, the ignition has to be configured using the ignition line, and in the inputs tap it must be set on the first input.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdV3SgpG9Gxj4HX3S-DS8RZww5Z1gKp9_YgDpQQaxQeMdroWMEjJN-Hw17gauRFTEU5kNVGIYm_w9VzMwerDkc0QPfzCUxWhniTICZH1h9Mop8nL33wx1wWTWMTpLL2hPLTVYczj4WUt9io9Y6TU2fV8irp?key=bZs4OqgxBiGnj_EN9_Rr1w)

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXffoA9aj2OZ4A-vlhv-Jf7N51lJLBrGi-PmK2ivbxuNbLjtZ-lwKzed7drV1I-MUYEyno1tlKXFbnHHYYcKT-pfk6AwaLpAiYWyxV6xbhLv-ujgAtkav_AOHsPQofyYvZjcq5sDFO-1cHX9xC-kLFkbRvf_?key=bZs4OqgxBiGnj_EN9_Rr1w)

If something it is wrong and ignition has not been selected either on the ignition or inputs tap, then the device will not save the settings.&#x20;

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdBW1gDv4pPpjRAWPKazgLiTQ37vlm55gVpncyJWhxHvRvHDU82VbW-eTRw0FAefCEjyDPwODsUH5e0VAS218NUYU5Q_BSIwMAYOI177ir11jpZ7KST_a9n940jShDlPVAmH0xRipygw4skCjd9rnZj4hpT?key=bZs4OqgxBiGnj_EN9_Rr1w)

Once we have voltage on physical input 1 (IN1), we would expect to see the change of state on input bit 1. However, the change bit will always be the ignition bit, so it can be verified through the air console.

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcQKZ5OxGfsDP4B24qTevIxlXNisYgA-pZNlV38RvtFZ-j9zv01Gl11LGbqCk6clzHr3iuwgFk7uqam2Uk-U_pMiVsCZWjQmvvs5UcnQYHb7Yt_ucjisuCv9JZOcVYKoujE848zoFf4wF__N5xuYyTUY7u3?key=bZs4OqgxBiGnj_EN9_Rr1w)

Therefore, in the Navixy platform it will be shown this way:

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfD-EgrCt5kphHWpt-y-hJZOnv4nOKEwoqc00kcpSs3I_lgHPTIiXmzAxcPKcBloVB-_JzrnASqNnkvuK0bsaQxPr2e5gVzUHO_fr9uGosBMtLTs4xjsmg0t6mGtmHDFAPkTSEEXMZ0ioZ6d9q-3B1JQq3p?key=bZs4OqgxBiGnj_EN9_Rr1w)

Particularly in this case, Navixy platform will be showing the ignition bit connected to the first physical input on the platform _input #1_, _input #2_ will not be used and _input #3_ will be showing the second physical input status on the device.

The suntech models that have an extra input on the Navixy platform for the ignition motion function are as follows:

* ST4315
* ST4315u
* ST4310w
* ST4215
* ST4215u
