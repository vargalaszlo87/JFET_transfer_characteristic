# JFET transfer characteristic.

This is a simple radio transmitter project. You can try the simulation files with ``LTspice`` and you can check the demo video too. Whit this circuit can you test an old radio. Let's try it, have a fun!

## Equation

$$I_{D} = \beta' * (V_{GS} - V_{TO}')^2 * (1 + \lambda * V_{DS})\\
V_{DS} = V_{DD} - I_{D} * (R_{D} + R_{S})\\
\beta' = \beta * (1 + \beta_{tce} * (T - T_{ref}))\\
V_{TO}' = V_{TO} * V_{TOtc} * (T - T_{ref})$$


\begin{multline*}
p(x) = 3x^6 + 14x^5y + 590x^4y^2 + 19x^3y^3\\ 
- 12x^2y^4 - 12xy^5 + 2y^6 - a^3b^3
\end{multline*}

## Example images

This image was created using Excel from raw output data.

![alt text](http://www.vargalaszlo.com/images/out/JFET_transfer_characteristic-01.jpg)

This image is the output of C program.

![alt text](http://www.vargalaszlo.com/images/out/JFET_transfer_characteristic-02.jpg)
