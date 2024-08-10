# JFET transfer characteristic.

This C project calculates the transfer characteristics of a JFET based on a SPICE model. In this case the model is the 2N3819 N-channel JFET. Optimization by Rd and Rs requires a nonlinear approximate solution. I use the newton method.

## SPICE model

.model 2N3819 NJF(Beta=1.304m Betatce=-.5 Rd=1 Rs=1 Lambda=2.25m Vto=-3 Vtotc=-2.5m Is=33.57f Isr=322.4f N=1 Nr=2 Xti=3 Alpha=311.7u Vk=243.6 Cgd=1.6p M=.3622 Pb=1 Fc=.5 Cgs=2.414p Kf=9.882E-18 Af=1 mfg=Vishay)

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

## Code

#include <stdio.h>
#include <stdlib.h>
#include <math.h>

typedef struct _jfet {
    double BETA;
    double V_TO;
    double LAMBDA;
    double R_D;
    double R_S;
    double V_TOtc;
    double BETA_tce;
} jfet;

typedef struct _calculation {
    double V_DD;
    double V_DS;
    double T;
    double T_ref;
    double V_GSStep;
    double solverTolerance;
    double solverMaxIteration;
} calculation;

typedef struct _characteristic {
    double I_D;
    double V_GS;
} characteristic;

double jfetTransferCharacteristic(double V_GS, double V_DS, double LAMBDA, double BETA, double V_TO) {
    return (V_GS < V_TO) ? 0.0 : ((BETA * pow(V_GS - V_TO, 2)) * (1 + LAMBDA * V_DS));
}

double solveI_D(calculation* cal, jfet* fet, double V_GSActual) {
    double
        I_DActual = 0.0,
        I_DPrevius,
        V_TOCorrected,
        BETACorrected;
    int iteration = 0;

    // correction
    V_TOCorrected = fet->V_TO + fet->V_TOtc * (cal->T - cal->T_ref);
    BETACorrected = fet->BETA * (1 + fet->BETA_tce * (cal->T - cal->T_ref));

    // Newton method
    do {
        I_DPrevius = I_DActual;
        cal->V_DS = cal->V_DD - I_DActual * (fet->R_D + fet->R_S);
        I_DActual = jfetTransferCharacteristic(V_GSActual, cal->V_DS, fet->LAMBDA, BETACorrected, V_TOCorrected);

        iteration++;
    } while (fabs(I_DActual - I_DPrevius) > cal->solverTolerance && iteration < cal->solverMaxIteration);

    return I_DActual;
}

int main()
{
    // 2N3819 JFET spice parameters (only necessary)
    jfet _2N3819;

    _2N3819.BETA = 1.304e-3;
    _2N3819.V_TO = -3.0;
    _2N3819.LAMBDA = 2.25e-3;
    _2N3819.R_D = 1;
    _2N3819.R_S = 1;
    _2N3819.BETA_tce = -0.5e-2;
    _2N3819.V_TOtc = -2.5e-3;

    // calculation setup
    calculation calc;

    calc.V_DD = 10;
    calc.T = 25;
    calc.T_ref = 26.85;
    calc.V_GSStep = 0.1;
    calc.solverTolerance = 1e-6;
    calc.solverMaxIteration = 100;

    // calculation
    characteristic out;

    out.V_GS = -3.0;
    printf ("V_GS[V]\tI_D[mA]\n");
    for (out.V_GS ; out.V_GS <= 0.0 ; out.V_GS += calc.V_GSStep) {
        out.I_D = solveI_D(&calc, &_2N3819, out.V_GS);
        printf ("%.2f\t%.4f\n",out.V_GS, out.I_D * 1000);
    }
    return 0;
}

```




```
