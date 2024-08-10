# JFET transfer characteristic.

This C project calculates the transfer characteristics of a JFET based on a SPICE model. In this case the model is the 2N3819 N-channel JFET. Optimization by Rd and Rs requires a nonlinear approximate solution. I use the newton method.

## Equation

$$I_{D} = \beta' * (V_{GS} - V_{TO}')^2 * (1 + \lambda * V_{DS})$$


$$V_{DS} = V_{DD} - I_{D} * (R_{D} + R_{S})$$


$$\beta' = \beta * (1 + \beta_{tce} * (T - T_{ref}))$$


$$V_{TO}' = V_{TO} * V_{TOtc} * (T - T_{ref})$$


## Example images

This image was created using Excel from raw output data.

![alt text](http://www.vargalaszlo.com/images/out/JFET_transfer_characteristic-01.jpg)

This image is the output of C program.

![alt text](http://www.vargalaszlo.com/images/out/JFET_transfer_characteristic-02.jpg)
