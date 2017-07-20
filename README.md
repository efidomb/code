def draw_lines(img, lines, color=[255, 0, 0], thickness=12, extrapolate=False):
    
    if extrapolate:

        left_slope = np.array([])
        left_x = np.array([])
        left_y = np.array([])

        right_slope = np.array([])
        right_x = np.array([])
        right_y = np.array([])

        for line in lines:
            for x1,y1,x2,y2 in line:
                slope = (y2-y1)/(x2-x1)
                if slope > 0:
                    right_y = np.append(right_y, min(y1, y2))
                    right_slope = np.append(right_slope, slope)
                    right_x = np.append(right_x, x1)
                else:
                    left_y = np.append(left_y, min(y1, y2))
                    left_slope = np.append(left_slope, slope)
                    left_x = np.append(left_x, x2)

        left_y = np.min(left_y)
        left_slope = np.average(left_slope)
        left_x = np.max(left_x)
        left_const = left_y - left_slope * left_x
        left_bottom = (img.shape[1] - left_const) / left_slope

        right_y = np.min(right_y)
        right_slope = np.average(right_slope)
        right_x = np.min(right_x)
        right_const = right_y - right_slope * right_x
        right_bottom = (img.shape[1] - right_const) / right_slope

        hight = min(left_y, right_y)
        if left_y > right_y: left_x = (right_y - left_const) / left_slope
        else: right_x = (left_y - right_const) / right_slope

        lines = [[(int(left_bottom), img.shape[1]), (int(left_x), int(hight))], \
                 [(int(right_bottom), img.shape[1]), (int(right_x), int(hight))]]

        cv2.line(img, (lines[0][0][0], lines[0][0][1]), (lines[0][1][0], lines[0][1][1]), color, thickness)
        cv2.line(img, (lines[1][0][0], lines[1][0][1]), (lines[1][1][0], lines[1][1][1]), color, thickness)

    else:
        for line in lines:
            for x1,y1,x2,y2 in line:
                cv2.line(img, (x1, y1), (x2, y2), color, thickness)
