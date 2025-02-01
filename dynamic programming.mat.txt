% Initialize variables
xx = zeros(1, 10);
yy = zeros(1, 10);
zz = zeros(1, 10);
XX = zeros(2, 10);
YY = zeros(1, 10);
l = zeros(1, 10);

% Initial values
w = 0.3; % weight
xx(1) = 1000;
yy(1) = 0;
zz(1) = 0;

for i = 1:1754
    % If condition for K(i)
    if K(i) == 0
        % Linear programming setup
        f = [w * p(i) / B_true(i) + (1 - w) * (-B_pre(i + 1) / B_true(i) + 50 / 49)];
        a = [50 / 49; -1];
        b = [xx(i); B_true(i) * zz(i)];
        [XX(2, i), YY(i + 1)] = linprog(f, a, b, [], [], [], []);
        
        XX(1, i) = 0; % Assign XX(1, i) to 0
        YY(i + 1) = -YY(i + 1) + (w - 1) * (xx(i) + G_pre(i + 1) * yy(i) + B_pre(i + 1) * zz(i));
        
        % Update variables
        xx(i + 1) = -50 / 49 * XX(2, i) + xx(i);
        yy(i + 1) = yy(i);
        zz(i + 1) = XX(2, i) / B_true(i) + zz(i);
        l(i + 1) = xx(i + 1) + yy(i + 1) * G_true(i + 1) + zz(i + 1) * B_true(i + 1) - (1 / 99) * XX(1, i) - (1 / 49) * XX(2, i);
        % Uncomment the following line if needed:
        % q(i + 1) = (q(i) * (i - 1) + Y(i + 1) - l(i + 1)) / i;
    else
        % Linear programming setup for K(i) != 0
        f = [w * q(i) / G_true(i) + (1 - w) * (-G_pre(i + 1) / G_true(i) + 100 / 99); 
             w * p(i) / B_true(i) + (1 - w) * (-B_pre(i + 1) / B_true(i) + 50 / 49)];
        a = [100 / 99, 50 / 49; -1, 0; 0, -1];
        b = [xx(i); G_true(i) * yy(i); B_true(i) * zz(i)];
        [XX(:, i), YY(i + 1)] = linprog(f, a, b, [], [], [], []);
        
        YY(i + 1) = -YY(i + 1) + (w - 1) * (xx(i) + G_pre(i + 1) * yy(i) + B_pre(i + 1) * zz(i));
        
        % Update variables
        xx(i + 1) = [-100 / 99, -50 / 49] * XX(:, i) + xx(i);
        yy(i + 1) = [1 / G_true(i), 0] * XX(:, i) + yy(i);
        zz(i + 1) = [0, 1 / B_true(i)] * XX(:, i) + zz(i);
        l(i + 1) = xx(i + 1) + yy(i + 1) * G_true(i + 1) + zz(i + 1) * B_true(i + 1) - (1 / 99) * XX(1, i) - (1 / 49) * XX(2, i);
        % Uncomment the following line if needed:
        % q(i + 1) = (q(i) * (i - 1) + Y(i + 1) - l(i + 1)) / i;
    end
end
