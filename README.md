     private async void TimerEventProcessor(object sender, EventArgs myEventArgs)
{
    DataTable dtPORR = new DataTable();

    try
    {
        dtPORR = PORSignal();
        dtPORR = await PORSignalAsync();

        lbl_Timer.Text = (Convert.ToInt32(lbl_Timer.Text) + 1).ToString();
        rAngle += 5;
        lAngle -= 5;

        // increase the rottion 
        //rAngle += 10;
        //lAngle -= 10;

        if (rAngle >= 360)
            rAngle = 0;
        if (lAngle <= -360)
            lAngle = 0;

        //POR1 rotation
        if (dtPORR.Rows[0][0].ToString() == "1")
        {
            circlePOR1.Invalidate();
            circlePOR1.angle = rAngle;

            circle135.Invalidate();
            circle135.angle = rAngle;

            circle165.Invalidate();
            circle165.angle = lAngle;

            circle167.Invalidate();
            circle167.angle = lAngle;

            circle155.Invalidate();
            circle155.angle = rAngle;

            circle158.Invalidate();
            circle158.angle = lAngle;

            circle154.Invalidate();
            circle154.angle = rAngle;

            circle133.Invalidate();
            circle133.angle = lAngle;

            circle132.Invalidate();
            circle132.angle = rAngle;

            circleRecoiler2.Invalidate();
            circleRecoiler2.angle = rAngle;

            circle77.Invalidate();
            circle77.angle = rAngle;
        }

        //POR2 rotation
        else if (dtPORR.Rows[0][1].ToString() == "1")
        {
            circlePOR2.Invalidate();
            circlePOR2.angle = rAngle;

            circle151.Invalidate();
            circle151.angle = rAngle;

            circle157.Invalidate();
            circle157.angle = lAngle;

            circle131.Invalidate();
            circle131.angle = lAngle;

            circle130.Invalidate();
            circle130.angle = lAngle;

            //circle129.Invalidate();
            //circle129.angle = lAngle;

            //circle128.Invalidate();
            //circle128.angle = lAngle;

            //circle156.Invalidate();
            //circle156.angle = lAngle;

            //circle153.Invalidate();
            //circle153.angle = lAngle;

            circleRecoiler2.Invalidate();
            circleRecoiler2.angle = rAngle;

            circle77.Invalidate();
            circle77.angle = rAngle;
        }

        circle129.Invalidate();
        circle129.angle = lAngle;

        circle128.Invalidate();
        circle128.angle = lAngle;

        circle156.Invalidate();
        circle156.angle = lAngle;

        circle153.Invalidate();
        circle153.angle = lAngle;

        circle35.Invalidate();
        circle35.angle = rAngle;

        circle36.Invalidate();
        circle36.angle = lAngle;

        circle150.Invalidate();
        circle150.angle = rAngle;

        circle166.Invalidate();
        circle166.angle = lAngle;

        circle148.Invalidate();
        circle148.angle = rAngle;

        circle140.Invalidate();
        circle140.angle = lAngle;

        circle117.Invalidate();
        circle117.angle = lAngle;

        circle115.Invalidate();
        circle115.angle = rAngle;

        circle113.Invalidate();
        circle113.angle = rAngle;

        circle111.Invalidate();
        circle111.angle = rAngle;

        circle109.Invalidate();
        circle109.angle = rAngle;

        circle104.Invalidate();
        circle104.angle = lAngle;

        circle108.Invalidate();
        circle108.angle = lAngle;

        circle107.Invalidate();
        circle107.angle = lAngle;

        circle106.Invalidate();
        circle106.angle = lAngle;

        circle96.Invalidate();
        circle96.angle = rAngle;

        circle94.Invalidate();
        circle94.angle = rAngle;

        circle99.Invalidate();
        circle99.angle = lAngle;

        circle162.Invalidate();
        circle162.angle = lAngle;

        circle161.Invalidate();
        circle161.angle = rAngle;

        circle146.Invalidate();
        circle146.angle = rAngle;

        circle138.Invalidate();
        circle138.angle = lAngle;

        circle114.Invalidate();
        circle114.angle = rAngle;

        circle112.Invalidate();
        circle112.angle = rAngle;

        circle110.Invalidate();
        circle110.angle = rAngle;

        circle105.Invalidate();
        circle105.angle = rAngle;

        circle103.Invalidate();
        circle103.angle = lAngle;

        circle102.Invalidate();
        circle102.angle = lAngle;

        circle1.Invalidate();
        circle1.angle = lAngle;

        circle2.Invalidate();
        circle2.angle = lAngle;

        circle3.Invalidate();
        circle3.angle = rAngle;

        circle4.Invalidate();
        circle4.angle = rAngle;

        circle5.Invalidate();
        circle5.angle = lAngle;

        circle6.Invalidate();
        circle6.angle = lAngle;

        circle7.Invalidate();
        circle7.angle = rAngle;

        circle8.Invalidate();
        circle8.angle = lAngle;

        circle9.Invalidate();
        circle9.angle = rAngle;

        circle10.Invalidate();
        circle10.angle = lAngle;

        circle11.Invalidate();
        circle11.angle = rAngle;

        circle87.Invalidate();
        circle87.angle = rAngle;

        circle116.Invalidate();
        circle116.angle = lAngle;

        circle139.Invalidate();
        circle139.angle = lAngle;

        circle32.Invalidate();
        circle32.angle = rAngle;

        circle37.Invalidate();
        circle37.angle = rAngle;

        circle38.Invalidate();
        circle38.angle = rAngle;

        circle39.Invalidate();
        circle39.angle = lAngle;

        circle40.Invalidate();
        circle40.angle = rAngle;

        circle41.Invalidate();
        circle41.angle = rAngle;

        circle43.Invalidate();
        circle43.angle = rAngle;

        circle44.Invalidate();
        circle44.angle = lAngle;

        circle45.Invalidate();
        circle45.angle = rAngle;

        circle46.Invalidate();
        circle46.angle = lAngle;

        circle47.Invalidate();
        circle47.angle = rAngle;

        circle15.Invalidate();
        circle15.angle = lAngle;

        circle30.Invalidate();
        circle30.angle = rAngle;

        circle14.Invalidate();
        circle14.angle = rAngle;

        circle51.Invalidate();
        circle51.angle = lAngle;

        circle52.Invalidate();
        circle52.angle = lAngle;

        circle61.Invalidate();
        circle61.angle = rAngle;

        circle62.Invalidate();
        circle62.angle = rAngle;

        circle63.Invalidate();
        circle63.angle = rAngle;

        circle64.Invalidate();
        circle64.angle = lAngle;

        circle56.Invalidate();
        circle56.angle = lAngle;

        circle66.Invalidate();
        circle66.angle = rAngle;

        circle23.Invalidate();
        circle23.angle = lAngle;

        //circle160.Invalidate();
        //circle160.angle = lAngle;

        //circle159.Invalidate();
        //circle159.angle = rAngle;

        //circle125.Invalidate();
        //circle125.angle = rAngle;

        //circle95.Invalidate();
        //circle95.angle = rAngle;

        //circle93.Invalidate();
        //circle93.angle = rAngle;

        //circle98.Invalidate();
        //circle98.angle = lAngle;

        //circle91.Invalidate();
        //circle91.angle = rAngle;

    }
    catch (Exception ex)
    {
        MessageBox.Show(ex.Message + "666");
    }

}
